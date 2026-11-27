# Spooky Author Identification

| | |
|---|---|
| Kaynak (Public Dataset) | [Kaggle - abhishek/spooky](https://www.kaggle.com/datasets/abhishek/spooky) *(Orijinal yarışma: [Spooky Author Identification](https://www.kaggle.com/c/spooky-author-identification))* |
| Tip | NLP / metin (3 sınıflı sınıflandırma) |
| Hedef | `author` (`EAP`, `HPL`, `MWS` — `19.579` cümle) |
| Durum | ✅ tamamlandı |

**Bu projede ne gösteriliyor:** Kapsamlı metin EDA (sınıf dağılımı, karakter/kelime uzunluğu, WordCloud), sızıntısız `TfidfVectorizer` (`ngram_range=(1,2)`, `sublinear_tf=True`, `min_df=3`), `MultinomialNB`, 5-Fold CV ve sınıf bazlı en karakteristik 10 kelimenin incelenmesi.

## Checklist'in hangi adımları kullanıldı

| # | Adım | Durum | Not |
|---|------|-------|-----|
| 1 | [Problem, metrik, baseline](../../guide/01_problem_metric_baseline.md) | ➖ kısmi / eksik | Accuracy ve `classification_report` var; `DummyClassifier` ve yarışma metriği `multi-class log-loss` eksik |
| 2 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | ✅ uygulandı | Duplicate kontrolü, küçük harf, noktalama, stop-word, `WordNetLemmatizer` |
| 3 | [EDA ve sızıntı avı](../../guide/03_eda_and_leakage.md) | ✅ uygulandı | Sınıf dağılımı, uzunluk histogramları, en sık kelimeler, WordCloud |
| 4 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | ✅ uygulandı | `stratify=y` ile %20 holdout split + sızıntısız `Pipeline` 5-Fold CV |
| 5 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | ➖ atlandı / eksik | Eksik değer `0` |
| 6 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | ➖ atlandı / eksik | `char_len`/`word_count` yalnızca EDA'da kullanıldı, modele verilmedi |
| 7 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | ✅ uygulandı | `TfidfVectorizer` yalnızca `X_train` üzerine `fit_transform` |
| 8 | [Özellik seçimi](../../guide/08_feature_selection.md) | ➖ atlandı / eksik | |
| 9 | [Dengesizlik ve pipeline](../../guide/09_imbalance_and_pipeline.md) | ✅ uygulandı | 5-Fold CV `Pipeline` içinde çalıştırıldı |

## Sonuçlar (Holdout Doğrulama — %20 `train_test_split` + 5-Fold CV)

> **Not:** `Holdout Accuracy` değeri `train.csv` içinden `train_test_split(..., test_size=0.2, stratify=y, random_state=42)` ile ayrılan **%20 holdout doğrulama seti** (`3.916` cümle) üzerinde hesaplanmıştır.

| Model | Train Accuracy | Holdout Accuracy | 5-Fold CV Accuracy | Macro F1 |
|---|---|---|---|---|
| MultinomialNB (TF-IDF n-gram 1-2) | **%86.10** | **%80.31** | **%79.68** (`0.7968 ± 0.0049`) | **0.80** |

## Bilinen Sorunlar ve Eksikler
- Yalnızca tek bir model (`MultinomialNB`, varsayılan `alpha=1.0`) eğitilmiştir; alternatif model (ör. LogisticRegression / LinearSVC) veya hiperparametre karşılaştırması yapılmamıştır.
- Yarışmanın resmi metriği olan `multi-class log-loss` hesaplanmamıştır.

## Veri ve Çalıştırma

```bash
pip install -r ../../requirements.txt
kaggle datasets download -d abhishek/spooky
jupyter lab notebook.ipynb
```

Notebook Kaggle ortamında public `abhishek/spooky` veri seti ile doğrudan çalışır. Yerelde çalıştırmak için `train.csv` dosyasını `data/` klasörüne koyup dosya yolunu uyarlayabilirsiniz.
