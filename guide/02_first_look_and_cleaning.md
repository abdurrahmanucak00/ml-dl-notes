# 02 · İlk Temas ve Deterministik Temizlik

**Neden bu adım var?**
Veri setleri nadiren temiz gelir: sayı olması gereken sütunlar `"1.6L"` ya da `"1,250"` gibi metinler yüzünden `object` okunabilir, aynı satır iki kez kayıtlı olabilir veya `PassengerId` gibi kimlik sütunları modele sızıp ezberlemeye (overfitting) yol açabilir. Bu adımdaki temizlikler satırlar arası istatistik (ortalama/medyan) gerektirmediği için veri bölünmeden önce güvenle yapılabilir.

## Temel (Önce Bunu Uygula)

### 1. İlk 5 komutla veriyi tanı

```python
df.shape                                      # Satır ve sütun sayısı
df.info()                                     # Veri tipleri ve eksik değer (Non-Null) sayıları
df.describe().T                               # Sayısal özet: min, max, ortalama, medyan (50%)
df.describe(include="object").T               # Kategorik özet: benzersiz sınıf sayısı (unique)
df.isnull().sum()[df.isnull().sum() > 0]      # Sadece eksik değer içeren sütunlar
```

### 2. Gizli veri tipi hatalarını düzelt
- **Birim / sembol içeren sayılar:** `"1.6L"`, `"$450"`, `"%12"`, `"1,500"` gibi değerler yüzünden sayısal sütunlar `object` okunur.
```python
# Örnek: "1.6L" -> 1.6 (sayısal)
df["Engine"] = pd.to_numeric(df["Engine"].str.replace("L", "", regex=False), errors="coerce")
```
- **Eksik veri gizleyen özel kodlar:** `"?"`, `"-"`, `"N/A"`, `-1` veya `99999` gibi değerler gerçekte eksik veriyse `np.nan` yap.

### 3. Duplicate (tekrar) ve gereksiz sütunları temizle
- **Tam tekrar satırlar:** `df = df.drop_duplicates()`. *(Not: İş mantığı gereği aynı müşterinin iki farklı gerçek işlemi ise silme.)*
- **Kimlik (ID) sütunları:** `id`, `PassengerId`, `UUID`, `href` gibi her satırda benzersiz olan sütunlar örüntü taşımaz; model girdisinden çıkar (`df.drop(columns=["id"])`).
- **Sabit sütunlar:** Tek bir değere sahip (`df[col].nunique() <= 1`) sütunlar bilgi taşımaz, sil.
- **Metin standardizasyonu:** Kategorik değerlerde baştaki/sondaki boşlukları (`str.strip()`) ve büyük/küçük harf tutarsızlıklarını (`"Istanbul"` vs `"istanbul"`) eşitle. Mantıksız negatif değerleri (`yas = -5`) `np.nan` yap.

## İleri Seviye (İhtiyaç Olduğunda)

- **Bellek optimizasyonu (500 MB – 8 GB):** `df.memory_usage(deep=True).sum() / 1024**2` ile RAM kullanımına bak; uygun aralıklarda `int64 → int32/16`, `float64 → float32` ve düşük kardinaliteli `object → category` dönüşümü yap.
- **RAM'e sığmayan veriler (10 GB+):** Pandas yerine `Polars` veya `DuckDB` kullan, dosyaları `.csv` yerine `.parquet` formatında sakla.
- **Yarı-sabit sütunlar:** Satırların %99.9'u aynı değere sahipse azınlık değerinin hedefle güçlü ilişkisi olup olmadığını kontrol et; yoksa ele.

## Kendine Sor

- Hangi sütunlar yanlış veri tipinde (`object`) okundu ve bunları sayısala çevirdim mi?
- Kimlik (`id`) veya sabit sütunlardan modele yanlışlıkla giren var mı?
