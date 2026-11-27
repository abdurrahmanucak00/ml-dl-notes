# 00 · Veriden Modele Kontrol Listesi (Ana Rehber)

**Neden bu harita var?**
Makine öğrenmesi projelerinde en sık yapılan hata, veriyi anlamadan doğrudan model eğitmeye geçmek veya veri sızıntısı (data leakage) yüzünden gerçekte çalışmayacak sahte yüksek skorlar üretmektir. Bu rehber, ham bir CSV dosyasından güvenilir bir modele kadar izlemen gereken 11 adımı sırasıyla sunar.

> **İki altın kural her şeyden önce gelir**
> 1. Veriden istatistik öğrenen hiçbir şey (`Imputer`, `Scaler`, `Encoder`, `TfidfVectorizer`, `PCA`, `TargetEncoder`, grup istatistikleri) tüm veriye `fit` edilmez. Önce böl, sonra yalnızca eğitim (`train`) verisine `fit` et.
> 2. Her karar çapraz doğrulama (CV) skoruyla test edilir. Skoru iyileştirmeyen karmaşıklık pipeline'a girmez.

## Adımlar

| # | Adım | Ana Soru | Detay |
|---|------|----------|-------|
| 1 | Problem, metrik, baseline | Neyi, hangi metrikle tahmin ediyorum? Basit referans model kaç alıyor? | [01](01_problem_metric_baseline.md) |
| 2 | İlk temas ve temizlik | Veri ne kadar büyük, tipler doğru mu, duplicate / ID / sabit sütun var mı? | [02](02_first_look_and_cleaning.md) |
| 3 | EDA ve sızıntı avı | Hedef nasıl dağılmış, ilişkiler ne, tahmin anında bilinemeyecek sütun var mı? | [03](03_eda_and_leakage.md) |
| 4 | Bölme ve doğrulama | Hangi doğrulama stratejisi gerçek senaryoyu taklit eder? | [04](04_splitting_and_validation.md) |
| 5 | Eksik değer ve aykırı değer | Eksiklik neden var? Uç değer veri hatası mı, gerçek sinyal mi? | [05](05_missing_values_and_outliers.md) |
| 6 | Özellik mühendisliği | Alan bilgisinden modele yardımcı yeni sinyaller üretebilir miyim? | [06](06_feature_engineering.md) |
| 7 | Encoding, scaling, vektörleme | Seçtiğim model ailesi sayısal/kategorik/metin veriyi hangi formatta ister? | [07](07_encoding_and_scaling.md) |
| 8 | Özellik seçimi | Hangi sütunlar modele katkı sağlamak yerine gürültü ekliyor? | [08](08_feature_selection.md) |
| 9 | Dengesizlik ve pipeline | Sınıflar dengesiz mi? Tüm ön işleme adımları tek `Pipeline` içinde mi? | [09](09_imbalance_and_pipeline.md) |
| 10 | Model seçimi ve tuning | Hangi modelle başlamalı, overfitting nasıl anlaşılır, hiperparametre nasıl aranır? | [10](10_modeling_and_tuning.md) |
| 11 | Hata analizi ve kaydetme | Model en çok nerede yanılıyor ve eğitilen pipeline nasıl kaydedilir? | [11](11_error_analysis_and_export.md) |

## Proje Tipine Göre Kritik Adımlar

| Proje Tipi | Mutlaka Uygula | Genelde Atlanır |
|------------|----------------|-----------------|
| Tabular sınıflandırma | 1, 2, 3, 4, 5, 7, 9, 10, 11 | Dengeli veride 9'un dengesizlik kısmı |
| Tabular regresyon | 1, 2, 3, 4, 5, 6, 7, 10, 11 | 9 (sınıf dengesizliği kısmı) |
| Dengesiz sınıflandırma | 1, 3, 4, 7, 9, 10, 11 | 6 (zaman serisi kısımları) |
| NLP (metin) | 1, 2, 3, 4, 7 (metin vektörleme), 9, 10, 11 | 5 (sayısal outlier), 8 |

## Her Proje Notebook'unda Önerilen Not Şablonu

```markdown
**Adım:** 5 – Eksik değer
**Gözlem:** (veride ne gördüm, somut sayıyla)
**Karar:** (ne yaptım)
**Neden:** (tek cümle, kendi sözlerimle)
```

## Proje Bitmeden Son Kontrol

- [ ] `train_test_split` / CV, imputation, scaling ve encoding'den önce yapıldı
- [ ] İstatistik öğrenen tüm dönüşümler `Pipeline` / `ColumnTransformer` içinde
- [ ] Majority / `Dummy` baseline ile karşılaştırma tablosu var
- [ ] `random_state` sabit, `Restart & Run All` hatasız tamamlanıyor
- [ ] Uygulanmayan adımlar dürüstçe belirtildi
- [ ] Modelin en çok yanıldığı örnekler incelendi
