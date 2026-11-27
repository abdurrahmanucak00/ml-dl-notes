# 07 · Encoding, Scaling ve Metin Vektörleme

**Neden bu adım var?**
Makine öğrenmesi modelleri metinleri (`"Manual"`, `"Diesel"`, `"deprem tweeti"`) doğrudan okuyamaz; bunları sayılara dönüştürmek (encoding / vektörleme) gerekir. Ayrıca bazı algoritmalar sayısal sütunların ölçeğine çok duyarlıdır: `Kilometre` `100.000` iken `Motor_Hacmi` `1.6` olursa, ölçeklendirme yapılmayan doğrusal veya mesafe bazlı modeller büyük sayılı sütuna gereğinden fazla önem verir.

> **Altın kural:** Encoder, Scaler ve `TfidfVectorizer` yalnızca `X_train` üzerinde `fit_transform`, doğrulama ve test üzerinde `transform` edilir.

## Temel (Önce Bunu Uygula)

### A. Kategorik Kodlama (Encoding)

| Sütun Tipi | Örnek | Önerilen Yöntem | Not |
|------------|-------|-----------------|-----|
| İkili (2 sınıf) | `Evet / Hayır` | `0 / 1` eşleme | |
| Sıralı (Ordinal) | `Düşük < Orta < Yüksek` | `OrdinalEncoder` | Alfabetik değil, anlam sırasıyla |
| Sırasız (Nominal, 2–15 sınıf) | `Yakıt Tipi`, `Şehir` | `OneHotEncoder(handle_unknown="ignore")` | `handle_unknown="ignore"` testte yeni sınıf gelince çökmeyi önler |

```python
from sklearn.preprocessing import OneHotEncoder

# DOĞRU: pd.get_dummies'i tüm veriye uygulamak yerine OneHotEncoder'ı train'e fit et
ohe = OneHotEncoder(handle_unknown="ignore", sparse_output=False)
X_train_cat = ohe.fit_transform(X_train[cat_cols])
X_val_cat   = ohe.transform(X_val[cat_cols])
```

### B. Ölçeklendirme (Scaling) — Hangi Model İster?

| Model Ailesi | Scaling Gerekli mi? |
|--------------|---------------------|
| Ağaç tabanlılar (`RandomForest`, `XGBoost`, `LightGBM`, `CatBoost`) | **Hayır** (eşik değerlerine bakar, ölçekten etkilenmez) |
| Doğrusal (`Linear/Ridge/Lasso/Logistic`), `SVM`, `KNN`, `PCA`, Sinir Ağları | **Evet (Zorunlu)** |

- **Varsayılan seçim:** `StandardScaler` (ortalamayı `0`, standart sapmayı `1` yapar).
- **Uç değerler bırakıldıysa:** `RobustScaler` (medyan ve IQR kullanır, uç değerden etkilenmez).
- **Belirli aralık `[0, 1]` gerekiyorsa:** `MinMaxScaler`.

### C. Metin Vektörleme (NLP İçin)
1. **Regex temizleme sırası kritik:** Önce URL (`http\S+`), `@mention` ve `#hashtag` temizlenir; **ondan sonra** küçük harf ve noktalama temizliği yapılır. Aksi halde `@` ve `#` önceden silindiği için regex eşleşmez.
2. **TF-IDF baseline:** Transformer'lara geçmeden önce `TfidfVectorizer` + `MultinomialNB` veya `LogisticRegression` güçlü ve hızlı bir başlangıç verir:

```python
from sklearn.feature_extraction.text import TfidfVectorizer

tfidf = TfidfVectorizer(ngram_range=(1, 2), max_features=5000, min_df=3, sublinear_tf=True)
X_train_vec = tfidf.fit_transform(X_train_text)   # Yalnızca train'e fit!
X_val_vec   = tfidf.transform(X_val_text)         # Val/test'e sadece transform!
```

## İleri Seviye (İhtiyaç Olduğunda)

- **Yüksek kardinaliteli nominal sütunlar (15+ sınıf):** One-Hot yerine `Frequency / Count Encoding` veya CV içinde smoothing'li `TargetEncoder` kullan (tüm veride Target Encoding yapma: sızıntıdır).
- **Nadir sınıflar:** Eğitim setinde frekansı %1'in altında kalan kategorileri `"Rare"` altında topla.
- **CatBoost / Derin Öğrenme:** CatBoost kategorik sütunları `cat_features` ile doğrudan alır; derin öğrenmede `nn.Embedding` kullanılır.
- **Çarpık finansal sütunlar:** Önce `np.log1p` veya `Yeo-Johnson`, ardından `StandardScaler`.

## Kendine Sor

- Seçtiğim model ailesi için scaling gerekli miydi?
- Test setinde eğitimde hiç görülmemiş bir kategori gelirse kodum çökmeden çalışıyor mu?
