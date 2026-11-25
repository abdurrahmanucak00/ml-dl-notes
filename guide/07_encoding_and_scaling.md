# 07 · Encoding ve Scaling

## A. Kategorik kodlama

| Tip | Kardinalite | Yöntem | Not |
|-----|-------------|--------|-----|
| Binary | 2 | 0/1 mapping | |
| Ordinal | sıralı | Ordinal encoding | Alfabetik değil, iş mantığı sırasıyla (`Po < Fa < TA < Gd < Ex`) |
| Nominal | düşük (2–15) | One-Hot | `handle_unknown="ignore"`; doğrusal modelde `drop="first"` |
| Nominal | yüksek (15+) | Frequency/Count, OOF Target Encoding | Target encoding CV içinde ve smoothing'li |
| Nominal | herhangi, derin öğrenme | Entity embedding | `nn.Embedding` |
| Nominal | herhangi, CatBoost | Native `cat_features` | Encoding gerekmez |

- **Rare label:** frekansı %1'in veya ~10–20 gözlemin altındaki kategorileri `"Rare"` yap.
- Test'te görülmemiş kategori pipeline'ı çökertmemeli.
- Target encoding'i tüm veride yapma: sızıntıdır.

## B. Scaling

| Model ailesi | Scaling |
|--------------|---------|
| XGBoost, LightGBM, CatBoost, RF, ExtraTrees | Gerekmez |
| Ridge/Lasso/Logistic, SVM, KNN, K-Means, PCA, MLP/DL | Zorunlu |

| Scaler | Ne zaman |
|--------|----------|
| `StandardScaler` | Yaklaşık normal, belirgin uç değer yok |
| `RobustScaler` | Uç değerler bırakıldıysa (medyan + IQR) |
| `MinMaxScaler` | `[0,1]` aralığı gerekiyorsa (sigmoid/tanh, piksel) |
| `MaxAbsScaler` | Seyrek matris (sıfırları korur) |
| `log1p`/Yeo-Johnson + `StandardScaler` | Çarpık fiyat/gelir türü sütunlar |

Scaler `fit` sadece train'de. Pipeline içine koy.

## C. Metin vektörleme (NLP projeleri için)

- `TfidfVectorizer`: `ngram_range=(1,2)`, `min_df`, `sublinear_tf=True`; char n-gram ayrı dene.
- Vektörleyici `fit` sadece train metninde.
- Lineer model (LogReg, LinearSVC) + TF-IDF güçlü bir baseline'dır; transformer'dan önce bunu kur.

## Kendine sor

- Hangi model ailesini kullanıyorum ve scaling neden gerekli/gereksiz?
- Yüksek kardinaliteli sütunda hangi encoding'i seçtim, sızıntıyı nasıl önledim?
