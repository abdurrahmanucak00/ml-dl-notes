# 03 · EDA ve Veri Sızıntısı Avı

Amaç: verinin hikayesini anlamak, sızıntıyı yakalamak, train/test farkını görmek.

## 1. Hedef değişken analizi

**Regresyon**
- `skew()` ve `kurtosis()` ölç. Sağ çarpıksa (`skew > 0.75`) ve hedef pozitifse `log1p` düşün.
- Hedefin büyük kısmı `0` ise (zero-inflated) iki aşamalı model: önce `>0 mı?`, sonra tutar.

**Sınıflandırma**
- Sınıf oranları. Bir sınıf %1'in altındaysa aşırı dengesizlik, bkz. [09](09_imbalance_and_pipeline.md).

## 2. Tek değişkenli bakış

- Sayısal: histogram, boxplot, `df.skew()`.
- Kategorik: `value_counts()`, nadir sınıflar, kardinalite.
- `mean ≈ median` simetri, `mean > median` sağ çarpıklık ipucu. Heuristiktir, sayıyı `skew()` ile doğrula.

## 3. İki değişkenli bakış

- Sayısal–sayısal: Pearson (doğrusal), Spearman (monotonik).
- Çoklu doğrusallık: `|r| > 0.85` veya VIF > 10. Doğrusal modellerde sorun, ağaçlarda yorumlanabilirliği böler.
- Kategorik–hedef: grup bazlı hedef ortalaması / oranı.

## 4. Sızıntı (leakage) kontrolü

1. **Gelecekten gelen bilgi:** tahmin anında elimde olmayacak sütun.
2. **Gerçek olamayacak kadar güçlü özellik:** hedefle `|r| > 0.9` veya tek başına neredeyse mükemmel skor.
3. **Grup sızıntısı:** aynı kişi/cihaz hem train hem validation'da.
4. **Preprocessing sızıntısı:** tüm veride `fit` edilen scaler/imputer/encoder. En sık yapılan hatadır.

Kural: her sütun için "bu bilgi tahmin anında var mı?" diye sor.

## 5. Adversarial validation (ayrı test seti varsa)

Train = 0, test = 1 etiketiyle birleştir, sınıflandırıcı eğit.
- ROC-AUC ≈ 0.5: dağılım aynı.
- ROC-AUC > 0.75: covariate shift. Önem skoru en yüksek sütunlar (genelde `ID`, tarih) kaymanın kaynağıdır.

## Kendine sor

- EDA'dan hangi üç karar çıktı (log dönüşümü, sütun atma, encoding seçimi gibi)?
- Sızıntı şüphesi olan sütun var mıydı, nasıl doğruladım?
