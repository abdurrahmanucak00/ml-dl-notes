# 00 · Veriden Modele Kontrol Listesi (Ana Rehber)

Bu dosya haritadır. Her adımın detayı kendi dosyasında; projelerde ilgili adıma `Adım X` diye referans verilir.

> **İki kural her şeyden önce gelir**
> 1. Veriden istatistik öğrenen hiçbir şey (`Imputer`, `Scaler`, `Encoder`, `PCA`, `TargetEncoder`, groupby istatistikleri) tüm veriye `fit` edilmez. Önce böl, sonra sadece train'e fit et.
> 2. Her karar CV skoruyla test edilir. Skoru iyileştirmeyen karmaşıklık pipeline'a girmez.

## Adımlar

| # | Adım | Ana soru | Detay |
|---|------|----------|-------|
| 1 | Problem, metrik, baseline | Neyi, hangi metrikle tahmin ediyorum? Aptal model kaç alıyor? | [01](01_problem_metric_baseline.md) |
| 2 | İlk temas ve temizlik | Veri ne kadar büyük, tipler doğru mu, duplicate/ID/sabit sütun var mı? | [02](02_first_look_and_cleaning.md) |
| 3 | EDA ve sızıntı avı | Hedef nasıl dağılmış, ilişkiler ne, sızıntı var mı? | [03](03_eda_and_leakage.md) |
| 4 | Bölme ve doğrulama | Hangi CV stratejisi gerçek senaryoyu taklit eder? | [04](04_splitting_and_validation.md) |
| 5 | Eksik değer ve aykırı değer | Eksiklik neden var? Uç değer hata mı sinyal mi? | [05](05_missing_values_and_outliers.md) |
| 6 | Özellik mühendisliği | Alan bilgisinden hangi sinyalleri üretirim? | [06](06_feature_engineering.md) |
| 7 | Encoding ve scaling | Model ailesi neye ihtiyaç duyuyor? | [07](07_encoding_and_scaling.md) |
| 8 | Özellik seçimi | Hangi sütunlar gürültü? | [08](08_feature_selection.md) |
| 9 | Dengesizlik ve pipeline | Sınıflar dengesiz mi? Her şey tek pipeline'da mı? | [09](09_imbalance_and_pipeline.md) |
| 10 | Model, tuning, yorum | Birkaç model, OOF skor, hata analizi | proje notebook'larında |

## Proje tipine göre hangi adımlar kritik?

| Proje tipi | Mutlaka | Genelde atlanır |
|------------|---------|-----------------|
| Tabular sınıflandırma | 1, 2, 3, 4, 5, 6, 7, 8 | Dengesiz değilse 9 (imbalance kısmı) |
| Tabular regresyon | 1, 2, 3, 4, 5, 6, 7, 8 | 9 (imbalance) |
| Dengesiz sınıflandırma | 1, 3, 4, 7, 9 | 6 (zaman serisi kısımları) |
| NLP | 1, 2, 4, 7 (metin vektörleme), 9 | 5 (outlier), 8 |
| Görüntü / DL | 1, 4, 9 | Çoğu tabular adımı |

## Her proje notebook'unda md şablonu

Her adımın üstüne 4 satırlık bir md hücresi:

```
**Adım:** 5 – Eksik değer
**Gözlem:** (veride ne gördüm, somut sayıyla)
**Karar:** (ne yaptım)
**Neden:** (tek cümle, kendi sözlerimle)
```

Uygulanmayan adım da yazılır: "Adım 9 – imbalance: uygulanmadı, sınıflar %52/%48."

## Son kontrol (proje bitmeden)

- [ ] Split, imputation/scaling/encoding'den önce yapıldı
- [ ] Tüm dönüşümler `Pipeline` / `ColumnTransformer` içinde
- [ ] Baseline ile karşılaştırma tablosu var
- [ ] Seed sabit, `Restart & Run All` temiz çalışıyor
- [ ] Atlanan adımlar gerekçesiyle yazıldı
- [ ] Yapılan bir hata ve düzeltmesi anlatıldı
