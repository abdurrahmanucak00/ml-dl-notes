# 07 · Spooky Author Identification (Çok Sınıflı NLP)

| Özellik | Değer |
|---|---|
| Seviye | Orta–İleri |
| Problem Tipi | NLP — Çok Sınıflı Metin Sınıflandırma (3 Yazar) |
| Hedef Değişken | `author` (`EAP`, `HPL`, `MWS` — `19.579` cümle) |
| Veri Kaynağı | [Kaggle — abhishek/spooky](https://www.kaggle.com/datasets/abhishek/spooky) *(Orijinal yarışma: [Spooky Author Identification](https://www.kaggle.com/c/spooky-author-identification))* |

**Bu projede ne öğrenirsin?**
İkili NLP'den **çok sınıflı metin sınıflandırmaya ve model yorumlamaya** geçiş: kapsamlı metin EDA (uzunluk dağılımları, en sık kelimeler, WordCloud), `stratify=y`, `sublinear_tf=True` ve `min_df=3` ayarlı sızıntısız TF-IDF, 5-Fold CV ve `feature_log_prob_` ile her yazarın en karakteristik 10 kelimesini keşfetme.

## Rehber Eşleme Tablosu (`guide/`)

| # | Adım | Kapsam | Not |
|---|---|---|---|
| 01 | [Problem, metrik ve baseline](../../guide/01_problem_metric_baseline.md) | Kısmi | Accuracy ve `classification_report` var; `DummyClassifier` ve `multi-class log-loss` eksik |
| 02 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | Uygulandı | Duplicate kontrolü, küçük harf, noktalama, stop-word, `WordNetLemmatizer` |
| 03 | [EDA ve veri sızıntısı avı](../../guide/03_eda_and_leakage.md) | Uygulandı | Sınıf dağılımı, uzunluk histogramları, en sık kelimeler, WordCloud |
| 04 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | Uygulandı | `stratify=y` ile %20 holdout split + sızıntısız `Pipeline` 5-Fold CV |
| 05 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | Atlandı | Eksik değer `0` |
| 06 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | Atlandı | `char_len`/`word_count` yalnızca EDA'da kullanıldı, modele verilmedi |
| 07 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | Uygulandı | `TfidfVectorizer` yalnızca `X_train` üzerine `fit_transform` |
| 08 | [Özellik seçimi](../../guide/08_feature_selection.md) | Atlandı | |
| 09 | [Dengesiz veri ve pipeline](../../guide/09_imbalance_and_pipeline.md) | Uygulandı | 5-Fold CV `Pipeline` içinde çalıştırıldı |
| 10 | [Model seçimi ve tuning](../../guide/10_modeling_and_tuning.md) | Kısmi | Tek model (`MultinomialNB`, `alpha=1.0`), Train vs Holdout overfit kontrolü yapıldı |
| 11 | [Hata analizi ve kaydetme](../../guide/11_error_analysis_and_export.md) | Kısmi | Confusion Matrix + sınıf bazlı en karakteristik 10 kelime yorumlandı; kaydetme yok |

## Sonuçlar (Holdout Doğrulama — %20 `train_test_split` + 5-Fold CV)

> **Not:** `Holdout Accuracy` değeri `train.csv` içinden `train_test_split(..., test_size=0.2, stratify=y, random_state=42)` ile ayrılan **%20 holdout doğrulama seti** (`3.916` cümle) üzerinde hesaplanmıştır.

| Model | Train Accuracy | Holdout Accuracy | 5-Fold CV Accuracy | Macro F1 |
|---|---|---|---|---|
| MultinomialNB (TF-IDF n-gram 1-2) | **%86.10** | **%80.31** | **%79.68** (`0.7968 ± 0.0049`) | **0.80** |

## Bilinen Sorunlar ve Eksikler
- Yalnızca tek bir model (`MultinomialNB`, varsayılan `alpha=1.0`) eğitilmiştir; alternatif model (ör. `LogisticRegression` / `LinearSVC`) veya hiperparametre karşılaştırması yapılmamıştır.
- Yarışmanın resmi metriği olan `multi-class log-loss` hesaplanmamıştır.

## Veri ve Çalıştırma

```bash
pip install -r ../../requirements.txt
kaggle datasets download -d abhishek/spooky
jupyter lab notebook.ipynb
```

Notebook Kaggle ortamında public `abhishek/spooky` veri seti ile doğrudan çalışır. Yerelde çalıştırmak için `train.csv` dosyasını `data/` klasörüne koyup dosya yolunu uyarlayabilirsiniz.

---
[← Önceki Proje: 06 · Disaster Tweets NLP](../06_disaster_tweets_nlp/README.md) · [Sonraki Proje: 08 · ChatGPT Çok Dilli Yorumlar →](../08_chatgpt_multilingual_sentiment/README.md)
