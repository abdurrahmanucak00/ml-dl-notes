# 11 · Hata Analizi, Yorumlama ve Model Kaydetme

**Neden bu adım var?**
Tek bir skor (`F1 = 0.78` veya `RMSE = 1,366`) sana modelin **neden** yanıldığını söylemez. Modelin en büyük hataları yaptığı 10–20 örneğe bakmak (Error Analysis), bir sonraki iterasyonda neyi düzelteceğini — hangi özelliğin eksik olduğunu ya da veride etiket hatası olup olmadığını — doğrudan gösterir. Son olarak, çalışan `Pipeline`'ı tek dosya halinde kaydetmek modeli notebook dışına taşımanı sağlar.

## Temel (Önce Bunu Uygula)

### 1. Sınıflandırmada Hata Analizi (Confusion Matrix & Yanlış Örnekler)
- **False Positive (Yanlış Alarm):** Gerçekte `0`, model `1` dedi.
- **False Negative (Kaçırılan Vaka):** Gerçekte `1`, model `0` dedi (dengesiz veride genelde en kritik hata).

```python
import pandas as pd
from sklearn.metrics import classification_report, confusion_matrix

y_pred = best_model.predict(X_val)
print(classification_report(y_val, y_pred))
print(confusion_matrix(y_val, y_pred))

# En emin olduğu halde yanıldığı örneklere bak (Error Analysis)
errors = X_val.copy()
errors["gercek"] = y_val
errors["tahmin"] = y_pred
wrong = errors[errors["gercek"] != errors["tahmin"]]
print(f"Toplam hata: {len(wrong)} / {len(X_val)}")
wrong.head(10)
```

### 2. Regresyonda Hata Analizi (Artık / Residual Analizi)
`hata = gerçek - tahmin` farkını incele:
- En büyük mutlak hataya sahip 10 satırı yazdır (`abs(y_val - y_pred).sort_values(ascending=False)`).
- Model belirli bir aralıkta (ör. çok pahalı araçlarda veya uçaklarda) sistematik olarak düşük mü tahmin ediyor? Eğer öyleyse `log1p` hedef dönüşümü veya yeni bir alan özelliği gerekebilir.

### 3. Eğitilmiş Pipeline'ı Kaydetme ve Tekrar Kullanma (`joblib`)
Tüm ön işleme adımları `Pipeline` içinde olduğu için tek bir `.joblib` dosyası kaydetmek yeterlidir; tahmin anında ham DataFrame doğrudan verilir:

```python
import joblib

# Kaydet
joblib.dump(best_model, "model_pipeline.joblib")

# Yükle ve yeni (ham) veri üzerinde tahmin al
loaded_model = joblib.load("model_pipeline.joblib")
new_preds = loaded_model.predict(X_new_raw)
```

## İleri Seviye (İhtiyaç Olduğunda)

- **Yorumlanabilirlik (SHAP / Özellik Katsayıları):**
  - Doğrusal modellerde `coef_`, Naive Bayes metin modellerinde `feature_log_prob_` (her sınıfın en karakteristik kelimeleri) incelenir.
  - Ağaç tabanlı modellerde `SHAP` summary ve waterfall grafikleriyle hem genel hem tekil tahmin açıklamaları çıkarılır.
- **Olasılık Kalibrasyonu:** Modelin ürettiği `0.80` olasılığı gerçekte %80 oranında gerçekleşiyor mu? Karar eşiği veya risk puanlaması yapılacaksa `calibration_curve` ve `Brier Score` kontrol edilir.
- **Şema Kontrolü:** Canlı sistemde beklenen sütun sırası, eksik kolon veya yeni kategori gelmesi senaryoları için küçük bir birim testi yazılır.

## Kendine Sor

- Modelin en çok yanıldığı 10 örneğe baktığımda ortak bir örüntü (eksik bilgi, gürültülü metin, belirli bir alt grup) gördüm mü?
- Eğitilmiş `Pipeline`'ı ham bir test satırıyla tekrar yükleyip hatasız tahmin alabildim mi?
