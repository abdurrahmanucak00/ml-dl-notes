# 01 · Problem Tanımı, Metrik ve Baseline

Koda başlamadan önce problemi optimize edilebilir bir şeye çevir.

## 1. Problem tipi

- Regresyon, ikili / çok sınıflı sınıflandırma, metin sınıflandırma, görüntü sınıflandırma?
- Tahmin anında elimde hangi bilgi olacak? (Sızıntı kontrolünün ilk adımı, bkz. [03](03_eda_and_leakage.md))

## 2. Metrik seçimi

| Durum | Metrik | Not |
|-------|--------|-----|
| Regresyon, büyük hatalar pahalı | RMSE | Uç değerlere duyarlı |
| Regresyon, dayanıklılık | MAE | |
| Regresyon, oransal hata / sağ çarpık hedef | RMSLE, MAPE | Hedefi log'layıp RMSE de olur |
| Dengeli sınıflandırma | ROC-AUC, Log-Loss | Olasılık kalitesi önemliyse Log-Loss |
| Dengesiz sınıflandırma | PR-AUC, F-beta | Accuracy tek başına kullanılmaz |
| Çok sınıflı | Macro-F1, Log-Loss | Sınıf sayısı dengesizse macro |

Yarışma verisiyse yarışmanın metriğini kullan; veri seti projesinde kendin seç ve nedenini yaz.

## 3. Baseline

- `DummyClassifier` / `DummyRegressor`: en sık sınıf veya ortalama.
- Ham veriyle basit model: Ridge/Logistic veya hızlı bir LightGBM.
- Skorları bir tabloya yaz, sonraki her adım bu tabloya satır ekler:

| Deney | CV skor | Fark |
|-------|---------|------|
| Dummy | | |
| Ham veri + basit model | | |
| + eksik değer işleme | | |
| + feature engineering | | |

Bu tablo "geliştirilmiş çalışma"nın kanıtıdır.

## 4. Kendine sor

- Metriği iş bağlamında neden seçtim?
- Baseline'ı geçmeyen adımı pipeline'a koydum mu?
