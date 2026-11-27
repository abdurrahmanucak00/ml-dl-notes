# 08 · Özellik Seçimi ve Boyut İndirgeme

**Neden bu adım var?**
Veride ne kadar çok sütun varsa model o kadar iyi olur diye bir kural yoktur. Hedefle ilgisiz veya birbirinin kopyası sütunlar modelin gürültüyü ezberlemesine (overfitting) ve eğitim süresinin uzamasına neden olur. Özellik seçimi, yalnızca gerçekten sinyal taşıyan sütunları tutarak modeli sadeleştirir ve genelleme gücünü artırır.

> **Altın kural:** Her sütun eleme kararı CV skoruyla doğrulanır. Eleme sonrası CV skoru düşüyorsa o sütun geri alınır.

## Temel (Önce Bunu Uygula)

### 1. Hızlı temel eleme
- **Sabit sütunlar:** Varyansı sıfır olan (`nunique == 1`) sütunları çıkar.
- **İkiz sütunlar:** Birbiriyle korelasyonu `|r| > 0.98` olan iki sütundan hedefle ilişkisi daha zayıf olanı ele.

### 2. Permutation Importance (Validation üzerinde en güvenilir temel test)
Doğrulama setinde bir sütunun değerlerini rastgele karıştırıp skorun ne kadar düştüğüne bakar:
- Skor **çok düşüyorsa** → sütun model için çok önemlidir.
- Skor **değişmiyorsa veya artıyorsa** → sütun gürültüdür, elemeyi dene.

```python
from sklearn.inspection import permutation_importance

result = permutation_importance(pipe, X_val, y_val, n_repeats=5, random_state=42, scoring="f1")
for col, imp in sorted(zip(X_val.columns, result.importances_mean), key=lambda x: -x[1])[:10]:
    print(f"{col:25s}: {imp:.4f}")
```

*(Neden ağaçların yerleşik `feature_importances_` değeri tek başına yetmez? Çünkü ağaç bölünmeleri çok fazla benzersiz değere sahip sütunları yapay olarak daha önemli göstermeye eğilimlidir.)*

## İleri Seviye (İhtiyaç Olduğunda)

- **Gömülü yöntemler:** `Lasso` (L1 regularizasyon) gereksiz özellik katsayılarını tam sıfıra indirir; hızlı bir `LightGBM` ile `gain == 0` olan sütunlar elenebilir.
- **SHAP değerleri:** Her özelliğin tahmini yalnızca ne kadar değil, **hangi yönde** (artırıcı/azaltıcı) etkilediğini gösterir; iş mantığına ters sızıntı özelliklerini yakalamada çok etkilidir.
- **Gölge özellik testi (Boruta / Null Importance):** Sütunların karıştırılmış sahte kopyalarından daha düşük önem alan gerçek sütunları eler.
- **Boyut indirgeme:** Yoğun sayısal veride `PCA` (scaling sonrası, yalnızca `train`'e fit), seyrek TF-IDF matrislerinde `TruncatedSVD` (LSA). Görselleştirme için `t-SNE` / `UMAP` kullanılır (doğrudan model girdisi olarak önerilmez).

## Kendine Sor

- Elediğim sütunların listesi ve eleme öncesi/sonrası CV skoru farkı nedir?
- Yüksek önem gösteren bir sütun gerçekten iş mantığına uygun mu, yoksa gözden kaçan bir sızıntı mı?
