# 03 · EDA ve Veri Sızıntısı Avı

**Neden bu adım var?**
Keşifsel Veri Analizi (EDA) sadece güzel grafik çizmek değildir; modellemede alacağın kararları (hangi sütunu atacağım, log dönüşümü gerekir mi, veri dengesiz mi?) kanıta bağlamak içindir. Aynı aşamada yapılan **veri sızıntısı (data leakage) avı**, testte %99 görünüp gerçek hayatta çöken modelleri baştan engeller.

## Temel (Önce Bunu Uygula)

### 1. Hedef değişkeni (target) incele
- **Regresyon:** Histogram çiz ve `y.skew()` ölç. Ortalama medyandan çok büyükse ve `skew > 0.75` ise dağılım sağa çarpıktır (ör. ev veya uçak fiyatları).
- **Sınıflandırma:** `y.value_counts(normalize=True)` ile sınıf oranlarına bak. Bir sınıf çok küçükse (ör. %10–15 altı) dengesiz veri stratejisine geç (bkz. [09](09_imbalance_and_pipeline.md)).

### 2. Sütunların dağılımı ve hedefle ilişkisi
- **Sayısal değişkenler:** Histogram ve boxplot ile uç değerlere bak; korelasyon matrisiyle hedefle en ilişkili sütunları gör (`df.corr(numeric_only=True)`).
- **Kategorik değişkenler:** `value_counts()` ile sınıf dengesini ve nadir sınıfları gör; kategori bazlı hedef ortalamalarına bak (`df.groupby("Sex")["Survived"].mean()`).

### 3. Dört temel veri sızıntısı (Data Leakage) türü
Her sütun için şu soruyu sor: **"Bu bilgi tahmin yapacağım anda gerçekten elimde olacak mı?"**

1. **Gelecekten gelen bilgi (Look-ahead Bias):** Tahmin anında henüz oluşmamış bilgi.
   - *Gerçek örnek:* Banka telefon pazarlamasında `duration` (görüşme süresi) arama bitmeden bilinemez; arama öncesinde kimin abone olacağını tahmin ederken kullanılamaz.
2. **Hedeften türetilmiş sütunlar:** Hedefin doğrudan bir formülü veya sıralaması.
   - *Gerçek örnek:* Araç fiyat tahmininde `PriceRank` veya `PPY` (Price Per Year) doğrudan fiyattan hesaplanmıştır; çıkarılmalıdır.
3. **Preprocessing sızıntısı (en yaygın hata):** `Imputer`, `Scaler`, `TfidfVectorizer` veya `get_dummies`'i `train_test_split` öncesinde tüm veriye uygulamak.
```python
# YANLIŞ: Tüm veriden öğrenip sonra bölmek (test bilgisi eğitime sızar)
X_vec = tfidf.fit_transform(df["text"])
X_train, X_test, y_train, y_test = train_test_split(X_vec, y, test_size=0.2)

# DOĞRU: Önce böl, yalnızca X_train üzerinde fit et
X_train, X_test, y_train, y_test = train_test_split(df["text"], y, test_size=0.2)
X_train_vec = tfidf.fit_transform(X_train)
X_test_vec  = tfidf.transform(X_test)
```
4. **Grup sızıntısı:** Aynı müşteri, hasta veya cihaza ait satırların hem eğitim hem doğrulama setine düşmesi (bkz. [04](04_splitting_and_validation.md)).

## İleri Seviye (İhtiyaç Olduğunda)

- **Çoklu doğrusallık (Multicollinearity):** İki özellik arasında `|r| > 0.85` veya `VIF > 10` olması doğrusal modellerde katsayıları kararsızlaştırır; ağaç modellerinde tahmini bozmaz ama özellik önemini (feature importance) iki sütuna böler.
- **Sıfır yığılmalı (Zero-Inflated) hedef:** Regresyon hedefinin büyük bölümü tam `0` ise (ör. sigorta hasar tutarı) iki aşamalı model kur: (1) `> 0 mı?` sınıflandırma, (2) `> 0` olanlarda tutar tahmini.
- **Adversarial Validation:** Ayrı bir yarışma test seti varsa `train=0`, `test=1` etiketleyip basit bir sınıflandırıcı eğit. `ROC-AUC ≈ 0.50` ise dağılımlar benzerdir; `> 0.75` ise dağılım kayması (covariate shift) vardır.

## Kendine Sor

- EDA sonucunda aldığım 2–3 somut karar (sütun atma, log dönüşümü, metrik seçimi gibi) neler?
- Hedefle şüpheli derecede yüksek ilişkili (`|r| > 0.90`) veya tahmin anında bilinemeyecek bir sütun var mı?
