# 02 · İlk Temas ve Deterministik Temizlik

İstatistik gerektirmeyen, satırlar arası hesap istemeyen düzeltmeler. Bölmeden önce yapılabilir.

## 1. İlk bakış

```python
df.shape
df.memory_usage(deep=True).sum() / 1024**2   # MB
df.info()
df.describe().T
df.describe(include="object").T
```

| Çıktı | Ne öğretir |
|-------|-----------|
| `shape`, bellek | Pandas yeter mi, downcasting gerekir mi? |
| `info()` | Tipler, `Non-Null Count` ile eksik değer, yanlış tipler |
| `describe()` | min/max aralıkları, `mean` vs `50%` ile çarpıklık ipucu, sabit sütunlar (`std=0`) |
| `describe(include="object")` | Kardinalite (`unique`), baskın sınıf (`freq`) |

## 2. Bellek

- 500 MB – 8 GB: `int64 → int32/16`, `float64 → float32`, az benzersiz `object → category`.
- RAM'den büyük: Polars / DuckDB / Dask, `.parquet`.

## 3. Gizli tip bozuklukları

- Sayı görünümlü metin: `"1.250,50"`, `"₺"`, `"%"`, `"?"`, `"-"`.
- Tarih görünümlü metin ve Unix timestamp.
- Sentinel değerler (`-1`, `999`, `0`) gerçekte eksik veri olabilir.

## 4. Duplicate

- Tam tekrar: `df.drop_duplicates()`.
- İş mantığı tekrarı: ID hariç aynı satırlar sistem hatası mı, gerçek davranış mı? Gerçekse silme.

## 5. Gereksiz sütunlar

- Benzersiz ID sütunları modele girmez (sıralı ID zaman bilgisi taşıyorsa önce incele).
- Sabit sütun (`nunique == 1`) sil.
- Yarı-sabit (%99.9 aynı) işaretle, hedefle ilişkisi güçlü değilse ele.

## 6. Metin standardizasyonu

- `strip()`, büyük/küçük harf, Türkçe karakter tutarsızlığı (`İ/i`, `I/ı`).
- Mantıksız değerler (`yaş = -5`) `NaN` yap.

## Kendine sor

- Hangi sütunlar yanlış tipte okundu?
- Silinen satır/sütun sayısı ve gerekçesi neydi?
