# 05 · Eksik Değer ve Aykırı Değer

Tüm istatistikler (median, sınır, quantile) **sadece train'den** hesaplanır.

## A. Eksik değer

### 1. Mekanizmayı teşhis et
- **MCAR:** eksiklik hiçbir şeye bağlı değil.
- **MAR:** başka bir gözlenen değişkene bağlı.
- **MNAR:** eksikliğin kendisi değerin bilgisini taşıyor.

Ayrıca bak: hangi sütunlar **birlikte** eksik? (Aynı sayıda dolu olan sütun grupları genelde aynı nedene bağlıdır, örneğin "garaj yok" → tüm garaj sütunları eksik.)

### 2. Karar matrisi

| Durum | Yöntem |
|-------|--------|
| NaN = "yok" anlamında (yapısal) | Mantıksal değer: `"None"`, `0`, `-1` |
| MNAR şüphesi, eksik oranı > %5 | Doldurmadan önce `col_is_missing` göstergesi ekle |
| Sayısal, simetrik | Mean |
| Sayısal, çarpık / uç değerli | Median |
| Kategorik | Mode veya ayrı `"Missing"` sınıfı |
| Gruplar arası fark büyük | Grup medyanı (train'den) |
| Değişkenler güçlü ilişkili | `KNNImputer` / `IterativeImputer` (ölçekleme gerekir, pahalı) |
| LightGBM / XGBoost / CatBoost | NaN'i olduğu gibi bırakmak da bir seçenek, CV ile karşılaştır |

Sütun %50+ eksikse: at, ya da sadece `is_missing` göstergesini tut. Ama önce eksikliğin sinyal taşıyıp taşımadığına bak.

## B. Aykırı değer

### 1. Tespit
- Normal dağılım: Z-score (`|z| > 3`).
- Çarpık dağılım: IQR (`Q1 - 1.5 IQR`, `Q3 + 1.5 IQR`) veya MAD.
- Çok değişkenli: Isolation Forest, LOF, Mahalanobis.
- Görsel: boxplot, scatter (örn. alan–fiyat grafiğinde kopan noktalar).

### 2. Sinyal mi hata mı?
Uç değer veri giriş hatası olabilir (`yaş = 250`) ya da gerçek ve önemli olabilir (dolandırıcılık). Alan bilgisiyle karar ver.

### 3. Müdahale (model ailesine göre)

| Model | Yaklaşım |
|-------|----------|
| Ağaç tabanlı | Kesin hata dışında dokunma |
| Doğrusal, KNN, SVM, NN | Winsorization (`np.clip`, train quantile'larıyla), log/Yeo-Johnson/Quantile dönüşümü |

- Satır silme sadece train'de ve hata olduğu kanıtlandıysa. **Test'ten asla satır silinmez.**
- Pozitif sağ çarpık: `log1p` / Box-Cox. Sıfır/negatif içeren: Yeo-Johnson.

## Kendine sor

- Eksiklik rastgele mi, bilgi mi taşıyor? Neyle doğruladım?
- Hangi uç değerleri tuttum, hangilerini neden çıkardım?
