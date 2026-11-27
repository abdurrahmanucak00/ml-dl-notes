# ml-dl-notes

Makine öğrenmesi ve derin öğrenmeyi **veriyle birlikte öğreten** not defteri. Her proje, ortak bir "veriden modele" rehberinin hangi adımlarını uyguladığını ve nelerin eksik/atlandığını şeffaf biçimde gösterir.

## Nasıl okunmalı

1. [`guide/00_data_to_model_checklist.md`](guide/00_data_to_model_checklist.md): ana harita.
2. `guide/01` – `09`: adım adım teori ve kontrol soruları.
3. `projects/`: rehberin gerçek Kaggle veri setlerinde uygulanışı. Her klasörde `README.md` (checklist, doğrulanmış skorlar ve bilinen sorunlar) ve çıktılı `notebook.ipynb`.

## Rehber

| Dosya | Konu |
|-------|------|
| [00](guide/00_data_to_model_checklist.md) | Ana kontrol listesi ve harita |
| [01](guide/01_problem_metric_baseline.md) | Problem, metrik, baseline |
| [02](guide/02_first_look_and_cleaning.md) | İlk temas ve temizlik |
| [03](guide/03_eda_and_leakage.md) | EDA ve sızıntı avı |
| [04](guide/04_splitting_and_validation.md) | Bölme ve doğrulama |
| [05](guide/05_missing_values_and_outliers.md) | Eksik ve aykırı değer |
| [06](guide/06_feature_engineering.md) | Özellik mühendisliği |
| [07](guide/07_encoding_and_scaling.md) | Encoding, scaling, vektörleme |
| [08](guide/08_feature_selection.md) | Özellik seçimi |
| [09](guide/09_imbalance_and_pipeline.md) | Dengesizlik ve pipeline |

## Tamamlanan Projeler (8)

> **Not:** Tüm değerlendirme metrikleri Kaggle üzerinde çalıştırılan notebook çıktılarından alınmıştır. Holdout skorları `train.csv` içinden `train_test_split(..., test_size=0.2, random_state=42)` ile ayrılan %20 doğrulama seti üzerindendir.

### NLP

| # | Proje | Yöntem | Doğrulanmış Skor | Durum |
|---|-------|--------|------------------|-------|
| 04 | [Disaster Tweets](projects/04_disaster_tweets_nlp) | Regex temizleme, TF-IDF Pipeline, `GridSearchCV` (NB vs XGBoost) | 4-Fold CV Acc: `0.7886` · Holdout Acc: `0.7981` · Macro F1: `0.78` | ✅ |
| 05 | [Spooky Author Identification](projects/05_spooky_author_identification) | EDA, WordCloud, sızıntısız TF-IDF, `MultinomialNB`, 5-Fold CV | Train Acc: `%86.10` · Holdout Acc: `%80.31` · 5-Fold CV: `0.7968 ± 0.0049` | ✅ |
| 08 | [ChatGPT App Reviews](projects/08_chatgpt_app_reviews) | `langdetect`, `translate_batch`, sızıntısız TF-IDF, `MultinomialNB` | Holdout Acc: `%79.42` *(baseline `%71.24`)* · Macro F1: `0.6937` · Neg Recall: `0.38` | ✅ |

### Tabular Sınıflandırma

| # | Proje | Yöntem | Doğrulanmış Skor | Durum |
|---|-------|--------|------------------|-------|
| 01 | [Titanic](projects/01_titanic) | `ColumnTransformer`, `Pipeline`, `XGBClassifier` | Holdout Acc: `0.7933` · Macro F1: `0.79` | ✅ |
| 02 | [Bank Dataset Binary Classification](projects/02_bank_binary_classification) | `Pipeline`, `RandomForest` vs `XGBoost` (`scale_pos_weight`), `GridSearchCV` | **Class 1 F1:** `0.67` · 3-Fold CV F1: `0.6668` · Holdout Acc: `%88.70` *(baseline `%87.86`)* | ✅ |
| 03 | [Introverts vs Extroverts](projects/03_introverts_vs_extroverts) | `ColumnTransformer`, `Pipeline`, `RandomForest`, 5-Fold CV | Holdout Acc: `0.9687` · 5-Fold CV F1: `0.9787 ± 0.0023` · ROC-AUC: `0.9649` | ✅ |

### Tabular Regresyon

| # | Proje | Yöntem | Doğrulanmış Skor | Durum |
|---|-------|--------|------------------|-------|
| 11 | [Audi A1 Listings](projects/11_audi_a1_listings) | Sızıntı sütunlarını çıkarma, birim temizleme, `LinearRegression` | Holdout R²: `0.9016` · RMSE: `1,366.36` · MAE: `1,140.62` | ✅ |
| 20 | [Airplane Price](projects/20_airplane_price) | Split sonrası imputation & `get_dummies`, `StandardScaler`, `LinearRegression` | Holdout R²: `0.7257` · RMSE: `505,871.19` · MAE: `358,087.70` | ✅ |

## Planlananlar

İleride eklenecek çalışma konuları (henüz başlanmadı):
- **NLP:** Turkish Text Categorization, TREMO Turkish Emotion, Chat Sentiment, Multilingual App Reviews, Emotion Detection from Text, Reddit Posts and Comments, Amazon Fine Food Reviews, IMDB 50K Reviews, Emotions for NLP, SMS Spam
- **Tabular Sınıflandırma:** Iris, Credit Card Default, Pokemon Stats
- **Tabular Regresyon:** Medical Cost, Student Marks, Car Sales, Student Grades
- **Görüntü / DL:** MNIST CSV

## Derin öğrenme

ML bölümü genişledikçe [`dl/`](dl) altında devam edecek.

## Kurulum

```bash
pip install -r requirements.txt
jupyter lab
```

Veri setleri repoya eklenmez; her proje `README.md` dosyasında Kaggle veri kaynağı verilmiştir. Notebook'lar Kaggle ortamında doğrudan çalışır; yerelde çalıştırmak için ilgili CSV dosyasını `data/` altına indirip dosya yolunu uyarlayabilirsiniz.
