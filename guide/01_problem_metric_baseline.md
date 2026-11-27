# 01 · Problem Tanımı, Metrik ve Baseline

**Neden bu adım var?**
Başarıyı neyle ölçeceğini bilmeden model eğitmek, hedef tahtasını görmeden ok atmaya benzer. Özellikle dengesiz verilerde yanlış metrik (örneğin yalnızca Accuracy) seçmek, aslında hiçbir şey öğrenmeyen bir modeli "çok başarılı" sanmana yol açar.

## Temel (Önce Bunu Uygula)

### 1. Problem tipini ve tahmin anını netleştir
- **Ne tahmin ediyorum?** Sayısal değer (regresyon), iki sınıf (ikili sınıflandırma), 3+ sınıf (çok sınıflı) veya metin sınıflandırma.
- **Tahmin anında elimde hangi bilgi olacak?** Gerçek dünyada tahmin yaparken henüz oluşmamış sütunlar modele giremez (bkz. [03 · EDA ve Veri Sızıntısı Avı](03_eda_and_leakage.md)).

### 2. Probleme uygun metrik seç

| Durum | Metrik | Neden? |
|-------|--------|--------|
| Regresyon (büyük hatalar kritik) | **RMSE** | Hataların karesini aldığı için büyük sapmaları sert cezalandırır, hedefle aynı birimdedir |
| Regresyon (uç değerlere dirençli) | **MAE** | Ortalama mutlak sapmayı doğrudan verir, yorumlaması kolaydır |
| Dengeli sınıflandırma | **Accuracy**, **ROC-AUC** | Sınıflar yaklaşık eşitse Accuracy anlamlıdır |
| Dengesiz sınıflandırma | **F1**, **PR-AUC** | Örnek: verinin %95'i `0`, %5'i `1` ise her şeye `0` diyen aptal model %95 Accuracy alır ama F1=`0.00` olur |
| Çok sınıflı sınıflandırma | **Macro-F1** | Her sınıfa eşit ağırlık verir; azınlık sınıfların gizlenmesini önler |

### 3. Baseline (referans model) kur
Hiçbir özellik mühendisliği yapmadan en basit referans skoru çıkar; bundan sonraki her iyileştirme bu çıtayı geçmek zorundadır:

```python
from sklearn.dummy import DummyClassifier
from sklearn.metrics import accuracy_score, f1_score

# Her zaman en sık sınıfı tahmin eden referans model
dummy = DummyClassifier(strategy="most_frequent")
dummy.fit(X_train, y_train)
y_dummy = dummy.predict(X_val)

print("Baseline Accuracy:", accuracy_score(y_val, y_dummy))
print("Baseline F1      :", f1_score(y_val, y_dummy))
```

Deneylerini küçük bir tabloda takip et:

| Deney | CV / Holdout Skor | Fark |
|-------|-------------------|------|
| Dummy (en sık sınıf / ortalama) | — | Referans |
| İlk basit model (Linear / NB / RF) | — | — |
| İyileştirilmiş pipeline | — | — |

## İleri Seviye (İhtiyaç Olduğunda)

- **Sağ çarpık regresyon hedefi:** Oransal hatalar önemliyse `RMSLE` veya `MAPE` kullan (veya hedefe `np.log1p(y)` uygulayıp `RMSE` ölç).
- **Olasılık kalitesi:** Sadece sınıf etiketi değil, tahmin edilen olasılığın doğruluğu önemliyse `Log-Loss` veya `Brier Score` takip et.
- **Maliyet duyarlı F-beta:** Yanlış negatif (ör. hastalığı kaçırmak) yanlış pozitiften daha pahalıysa Recall'a daha çok ağırlık veren `F2` (`beta=2`) kullan.

## Kendine Sor

- Seçtiğim metriği iş problemi açısından tek cümleyle savunabiliyor muyum?
- Kurduğum model basit `Dummy` / majority baseline'ı gerçekten anlamlı farkla geçiyor mu?
