# 06 · Özellik Mühendisliği (Feature Engineering)

**Neden bu adım var?**
Ham sütunlar çoğu zaman modelin kolayca yakalayamayacağı ilişkileri gizler. Örneğin bir aracın `Üretim_Yılı` ve `Kilometre` sütunlarını tek tek vermek yerine `Yıllık_Ortalama_Kilometre` oranını üretmek, modelin aracın ne kadar yıprandığını çok daha kolay öğrenmesini sağlar. İyi özellik mühendisliği, çoğu zaman model değiştirmekten daha büyük performans artışı getirir.

> **Altın kural:** Ürettiğin her yeni özellik tahmin anında hesaplanabilir olmalı ve CV skorunu gerçekten iyileştiriyorsa pipeline'da kalmalıdır.

## Temel (Önce Bunu Uygula)

### 1. Alan bilgisi oranları, toplamları ve farkları
- **Oranlar:** `borç / gelir`, `kilometre / araç_yaşı`, `tıklama / gösterim`. *(Sıfıra bölme hatasını önlemek için paydaya `+ 1e-5` ekle.)*
- **Toplamlar:** Ayrı sütunlardaki benzer bilgileri birleştir (`Toplam_Aile = SibSp + Parch + 1`, `Toplam_Alan = Kat1 + Kat2 + Bodrum`).
- **Süre / Yaş:** `Mevcut_Yıl - Yapım_Yılı`.
- **Varlık göstergeleri (`0/1`):** Çoğunlukla sıfır olan bir sütunda "var mı?" bayrağı (`has_cabin`, `has_pool`).

```python
# Örnek: Alan bilgisinden türetilen satır bazlı özellikler (sızıntı yaratmaz)
X["family_size"] = X["SibSp"] + X["Parch"] + 1
X["is_alone"]    = (X["family_size"] == 1).astype(int)
X["fare_per_person"] = X["Fare"] / (X["family_size"] + 1e-5)
```

### 2. Tarih ve metinden temel çıkarımlar
- **Tarih sütunu varsa:** Yıl, ay, haftanın günü, hafta sonu mu (`dt.dayofweek >= 5`).
- **Metin sütunu varsa:** Karakter uzunluğu, kelime sayısı, büyük harf oranı, ünlem/soru işareti sayısı.

## İleri Seviye (İhtiyaç Olduğunda)

- **Grup bazlı istatistikler (`groupby`):** Bir kategorik sütuna göre sayısal sütunun ortalama/medyan/std değerlerini çıkarıp göreli fark (`değer - grup_ortalaması`) üretmek. **Kritik:** Grup istatistikleri yalnızca `X_train`'den hesaplanıp `X_val`/`X_test`'e `merge` edilir; tüm veride hesaplamak sızıntıdır.
- **Döngüsel zaman kodlaması (doğrusal/NN modeller için):** `Saat 23` ile `Saat 00` yakınlığını korumak için `sin(2πx/T)` ve `cos(2πx/T)` (`T=24` saat, `T=12` ay).
- **Zaman serisi Lag / Rolling:** Veri `[grup, tarih]` sıralı olmalı; sızıntıyı önlemek için mevcut satırı hariç tut (`shift(1).rolling(7).mean()`).
- **Etkileşimler:** Kategorik kombinasyon (`sehir + "_" + meslek`) veya doğrusal modeller için `PolynomialFeatures(degree=2, interaction_only=True)`.
- **Türkçe metin notu:** Eklemeli dil yapısı nedeniyle kelime (`word`) n-gram yanında karakter (`char_wb`) n-gram denemeleri çoğu zaman ek kazanç sağlar.

## Kendine Sor

- Ürettiğim her yeni özellik gerçek tahmin anında elimde olacak sütunlardan mı hesaplanıyor?
- Eklediğim özelliğin CV skoruna katkısı, CV standart sapmasından (`std`) büyük mü?
