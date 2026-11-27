# 06 · Disaster Tweets (NLP İkili Sınıflandırma)

| Özellik | Değer |
|---|---|
| Seviye | Orta |
| Problem Tipi | NLP — İkili Metin Sınıflandırma |
| Hedef Değişken | `target` (`0` = afet değil, `1` = gerçek afet tweeti) |
| Veri Kaynağı | [Kaggle — NLP Getting Started](https://www.kaggle.com/c/nlp-getting-started) |

**Bu projede ne öğrenirsin?**
Tabular veriden **metin verisine (NLP)** geçiş: tweetlerde doğru sırada regex temizliği (URL, `@mention`, `#hashtag` önce; noktalama sonra), `ColumnTransformer` + `Pipeline` içinde sızıntısız `TfidfVectorizer` ve `GridSearchCV` ile `MultinomialNB` vs `XGBClassifier` karşılaştırması.

## Rehber Eşleme Tablosu (`guide/`)

| # | Adım | Kapsam | Not |
|---|---|---|---|
| 01 | [Problem, metrik ve baseline](../../guide/01_problem_metric_baseline.md) | Kısmi | CV Accuracy ve `classification_report` var; `DummyClassifier` baseline eksik |
| 02 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | Uygulandı | Tekrar eden tweetler çıkarıldı, regex + stop-word + lemmatization uygulandı |
| 03 | [EDA ve veri sızıntısı avı](../../guide/03_eda_and_leakage.md) | Atlandı | Yalnızca sınıf dağılımı incelendi |
| 04 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | Uygulandı | %20 holdout `train_test_split` + 4-Fold `GridSearchCV` |
| 05 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | Atlandı | `keyword` ve `location` sütunları çıkarıldı |
| 06 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | Atlandı | |
| 07 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | Uygulandı | `TfidfVectorizer(max_features=2000, ngram_range=(1,3))` `Pipeline` içinde |
| 08 | [Özellik seçimi](../../guide/08_feature_selection.md) | Atlandı | |
| 09 | [Dengesiz veri ve pipeline](../../guide/09_imbalance_and_pipeline.md) | Uygulandı | `ColumnTransformer` + `Pipeline` |
| 10 | [Model seçimi ve tuning](../../guide/10_modeling_and_tuning.md) | Uygulandı | `GridSearchCV` ile `MultinomialNB` vs `XGBClassifier` karşılaştırıldı |
| 11 | [Hata analizi ve kaydetme](../../guide/11_error_analysis_and_export.md) | Kısmi | `ConfusionMatrixDisplay` çizildi; yanlış tweet analizi ve kaydetme yok |

## Sonuçlar (Holdout Doğrulama — %20 `train_test_split` + 4-Fold CV)

> **Not:** `Holdout` skorları yarışma `test.csv` üzerinde değil, `train.csv` içinden `train_test_split(..., test_size=0.2, random_state=42)` ile ayrılan **%20 holdout doğrulama seti** (`1.501` tweet) üzerinde ölçülmüştür.

| Model | 4-Fold CV Accuracy | Holdout Accuracy | Holdout F1 (macro) | Class 1 F1 |
|---|---|---|---|---|
| MultinomialNB (TF-IDF n-gram 1-3) | **%78.86** (`0.7886`) | **%79.81** (`0.7981`) | **0.78** | `0.72` (`P: 0.86 / R: 0.62`) |
| XGBoost (TF-IDF n-gram 1-3) | `GridSearchCV` ile karşılaştırıldı | — | — | — |

## Bilinen Sorunlar ve Eksikler
- Yarışmanın resmi değerlendirme metriği F1 olmasına rağmen `GridSearchCV` içinde `scoring="accuracy"` kullanılmıştır.
- `keyword` ve `location` sütunları değerlendirilmeden çıkarılmıştır; yanlış sınıflandırılan tweetler üzerinde hata analizi yapılmamıştır.

## Veri ve Çalıştırma

```bash
pip install -r ../../requirements.txt
kaggle competitions download -c nlp-getting-started
jupyter lab notebook.ipynb
```

Notebook Kaggle ortamında doğrudan çalışır. Yerelde çalıştırmak için dosyaları `data/` klasörüne koyup dosya yollarını uyarlayabilirsiniz.

---
[← Önceki Proje: 05 · Banka Abonelik Tahmini](../05_bank_imbalanced_classification/README.md) · [Sonraki Proje: 07 · Spooky Author NLP →](../07_spooky_author_nlp/README.md)
