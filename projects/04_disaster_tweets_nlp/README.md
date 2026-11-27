# Natural Language Processing with Disaster Tweets

| | |
|---|---|
| Kaynak | [Kaggle - NLP Getting Started](https://www.kaggle.com/c/nlp-getting-started) |
| Tip | NLP / metin (ikili sınıflandırma) |
| Hedef | `target` (`0` = afet değil, `1` = gerçek afet tweeti) |
| Durum | ✅ tamamlandı |

**Bu projede ne gösteriliyor:** Tweet metinlerinde doğru sırada regex temizliği (URL, `@mention`, `#hashtag`), `ColumnTransformer` + `Pipeline` içinde sızıntısız `TfidfVectorizer` ve `GridSearchCV` ile `MultinomialNB` vs `XGBClassifier` karşılaştırması.

## Checklist'in hangi adımları kullanıldı

| # | Adım | Durum | Not |
|---|------|-------|-----|
| 1 | [Problem, metrik, baseline](../../guide/01_problem_metric_baseline.md) | ➖ kısmi / eksik | CV Accuracy ve `classification_report` var; `DummyClassifier` baseline eksik |
| 2 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | ✅ uygulandı | Tekrar eden tweetler çıkarıldı, regex + stop-word + lemmatization uygulandı |
| 3 | [EDA ve sızıntı avı](../../guide/03_eda_and_leakage.md) | ➖ atlandı / eksik | Yalnızca sınıf dağılımı incelendi |
| 4 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | ✅ uygulandı | %20 holdout `train_test_split` + 4-Fold `GridSearchCV` |
| 5 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | ➖ atlandı / eksik | `keyword` ve `location` sütunları çıkarıldı |
| 6 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | ➖ atlandı / eksik | |
| 7 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | ✅ uygulandı | `TfidfVectorizer(max_features=2000, ngram_range=(1,3))` `Pipeline` içinde |
| 8 | [Özellik seçimi](../../guide/08_feature_selection.md) | ➖ atlandı / eksik | |
| 9 | [Dengesizlik ve pipeline](../../guide/09_imbalance_and_pipeline.md) | ✅ uygulandı | `ColumnTransformer` + `Pipeline` + `GridSearchCV` |

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
