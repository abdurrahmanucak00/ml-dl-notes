# 05 · Eksik Değer ve Aykırı Değer

**Neden bu adım var?**
Çoğu klasik algoritma (`LinearRegression`, `LogisticRegression`, `SVM`, `KNN`) eksik (`NaN`) değerlerle hiç çalışmaz ve tek bir uç değer (`Fiyat = 100 milyon`) doğrusal regresyon çizgisini tamamen bozabilir. Ancak her eksik değeri körü körüne ortalamayla doldurmak ya da her uç değeri silmek de verideki gerçek sinyali yok eder.

> **Altın kural:** Ortalama, medyan, mod ve uç değer sınırları **yalnızca `X_train`'den hesaplanır**, ardından `X_val` ve `X_test`'e uygulanır.

## Temel (Önce Bunu Uygula)

### A. Eksik Değer (Imputation)

1. **Önce eksikliğin anlamını sor:**
   - **Yapısal eksiklik ("yok" anlamında):** Örneğin bir evin garajı yoksa `GarageArea = NaN` olur → medyanla değil `0` veya `"None"` ile doldur.
   - **Gerçekten bilinmeyen değer:** Aşağıdaki tabloyu kullan.

| Sütun Tipi / Durumu | Önerilen Yöntem | Neden? |
|---------------------|-----------------|--------|
| Sayısal, simetrik dağılım | **Mean** (ortalama) | Uç değer yoksa dağılımı korur |
| Sayısal, çarpık veya uç değerli | **Median** (medyan) | Uç değerlerden etkilenmez (en güvenli varsayılan) |
| Kategorik | **Mode** veya `"Missing"` sınıfı | `"Missing"` sınıfı eksikliğin kendisini de modele sinyal olarak verir |
| XGBoost / LightGBM / CatBoost | Olduğu gibi bırakmayı CV ile dene | Modern ağaç modelleri `NaN` değerleri doğal olarak yönlendirebilir |

```python
from sklearn.impute import SimpleImputer

# DOĞRU: İstatistiği yalnızca X_train'den öğren
num_imputer = SimpleImputer(strategy="median")
X_train_num = num_imputer.fit_transform(X_train[num_cols])
X_val_num   = num_imputer.transform(X_val[num_cols])
```

### B. Aykırı Değer (Outlier)

1. **Önce tespit et:** Histogram, boxplot veya **IQR kuralı** (`Q1 - 1.5*IQR` altı ve `Q3 + 1.5*IQR` üstü).
2. **Hata mı, gerçek sinyal mi?** `Yas = 250` kesin veri giriş hatasıdır; ancak lüks bir uçağın çok pahalı olması veya yüksek tutarlı bir kredi kartı harcaması (dolandırıcılık) **gerçek sinyaldir**.
3. **Model ailesine göre hareket et:**
   - **Ağaç modelleri (RF, XGBoost, LightGBM):** Kesin veri hatası dışında dokunma; ağaçlar eşik değerleriyle böldüğü için uç değerlerden çok az etkilenir.
   - **Doğrusal modeller, KNN, SVM, Sinir Ağları:** Uç değerlere çok duyarlıdır. Sınırlandırma (`clip` / Winsorization) veya `log1p` dönüşümü uygula.
   - **Test setinden asla satır silme:** Satır silme yalnızca eğitim setinde ve kanıtlanmış hatalarda yapılır.

## İleri Seviye (İhtiyaç Olduğunda)

- **Eksiklik mekanizmaları:**
  - *MCAR (Tamamen rastgele):* Sensör anlık kopması.
  - *MAR (Başka sütuna bağlı):* Genç kullanıcıların gelir alanını daha az doldurması.
  - *MNAR (Kendi değerine bağlı):* Çok yüksek gelirlilerin gelirini gizlemesi → doldurmadan önce `col_is_missing` (`0/1`) göstergesi ekle (`SimpleImputer(add_indicator=True)`).
- **Grup bazlı ve çok değişkenli doldurma:** Genel medyan yerine `train`'den hesaplanan grup medyanı veya `KNNImputer` / `IterativeImputer` (ölçekleme gerektirir, büyük veride yavaştır).
- **Çok değişkenli uç değer tespiti:** Tek başına normal görünüp kombinasyonu imkansız satırlar (`Yas = 19`, `Deneyim = 15 yıl`) için `IsolationForest` veya `LocalOutlierFactor`.
- **Dağılım dönüşümleri:** Pozitif sağ çarpık veride `np.log1p`, sıfır/negatif içeren çarpık veride `PowerTransformer(method="yeo-johnson")`.

## Kendine Sor

- Eksik değerleri `train_test_split` sonrasında, yalnızca `X_train` istatistikleriyle mi doldurdum?
- Çıkardığım veya sınırlandırdığım uç değerler gerçekten hata mıydı, yoksa modelin öğrenmesi gereken gerçek uç vakalar mıydı?
