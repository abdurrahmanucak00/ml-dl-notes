# 04 · Bölme Stratejileri ve Doğrulama

**Neden bu adım var?**
Bir modelin eğitim verisini ezberlemesi kolaydır; önemli olan **daha önce hiç görmediği veride** ne kadar iyi tahmin yaptığıdır. Veriyi doğru stratejiyle bölmezsen, doğrulama skorun gerçek performansı yansıtmaz. Bu adım imputation, scaling ve encoding'den **her zaman önce** gelir.

## Temel (Önce Bunu Uygula)

### 1. Tek Holdout (`train_test_split`) vs. Çapraz Doğrulama (Cross-Validation)
- **Holdout (`train_test_split`):** Veriyi örneğin %80 eğitim / %20 doğrulama olarak tek seferde böler. Hızlıdır ama küçük veri setlerinde hangi satırların %20'ye düştüğüne bağlı olarak şans payı taşır.
- **K-Fold Cross-Validation (CV):** Veriyi 5 parçaya böler; her seferinde 4 parça ile eğitip 1 parça ile test eder, sonunda 5 skorun ortalamasını ve standart sapmasını (`mean ± std`) verir. Modelin kararlılığını görmek için çok daha güvenilirdir.

### 2. Veri yapısına göre hangi bölme seçilir?

| Veri Yapısı | Strateji | Neden? |
|-------------|----------|--------|
| Bağımsız regresyon tablosu | `KFold(n_splits=5)` | Satırlar birbirinden bağımsızdır |
| Sınıflandırma (özellikle dengesiz) | `StratifiedKFold(n_splits=5)` | Her katmanda sınıf oranlarını birebir korur |
| Aynı kullanıcı/hastadan çoklu satır | `GroupKFold` / `StratifiedGroupKFold` | Aynı kişinin satırları hem train'de hem val'da yer almaz |
| Zaman serisi (tarihe bağlı veri) | `TimeSeriesSplit` | Gelecekteki veri geçmişi tahmin etmez |

### 3. Sızıntısız Cross-Validation kodu
`Pipeline` kullandığında `cross_val_score` her katmanda ön işlemeyi **otomatik olarak yalnızca o katmanın eğitim verisine** fit eder:

```python
from sklearn.model_selection import StratifiedKFold, cross_val_score

cv = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)
scores = cross_val_score(pipe, X_train, y_train, cv=cv, scoring="f1")

print(f"5-Fold CV F1: {scores.mean():.4f} ± {scores.std():.4f}")
```

### 4. Altın kurallar
- **Tekrarlanabilirlik:** `random_state=42` gibi sabit bir tohum (seed) kullan; aksi halde iki deney arasındaki farkın modelden mi rastgelelikten mi geldiğini anlayamazsın.
- **Standart sapmaya bak:** Yeni bir özellik CV ortalamasını `0.002` artırdı ama `std` `0.010` ise bu iyileşme gürültü olabilir.

## İleri Seviye (İhtiyaç Olduğunda)

- **Out-of-Fold (OOF) tahminler:** CV sırasında her satırın doğrulama katmanındayken üretilen tahminlerini saklamak. Karar eşiği optimizasyonu, ensemble (model birleştirme) ve hata analizi OOF tahminleriyle yapılır.
- **Dengesiz regresyon:** Sürekli hedefi `pd.qcut` ile 10 dilime bölüp `StratifiedKFold` uygula; böylece aşırı yüksek fiyatlar tek bir fold'a yığılmaz.
- **Nested CV:** Aynı CV üzerinde yüzlerce hiperparametre denemek doğrulama setine aşırı uyum (overfit) yaratabilir; bunu ölçmek için iç içe CV veya dokunulmamış ayrı bir holdout seti tut.

## Kendine Sor

- Veri setindeki satırlar gerçekten birbirinden bağımsız mı (zaman veya grup bağı var mı)?
- Tüm dönüşümler `train_test_split` / CV'den **sonra** mı çalışıyor?
