# ml-dl-notes

Makine öğrenmesi ve derin öğrenmeyi **veriyle birlikte öğreten** not defteri. Her proje, ortak bir "veriden modele" rehberinin hangi adımlarını neden uyguladığını gösterir. Atlanan adımlar da gerekçesiyle yazılır.

## Nasıl okunmalı

1. [`guide/00_data_to_model_checklist.md`](guide/00_data_to_model_checklist.md): ana harita.
2. `guide/01` – `09`: adım adım teori ve kontrol soruları.
3. `projects/`: rehberin gerçek verilerde uygulanışı. Her klasörde `README.md` (hangi adımlar kullanıldı) ve `notebook.ipynb`.

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

## Projeler

Yarışmalar (4) ve veri setleri (22). Durum sütununu bitirdikçe güncelle.

| # | Proje | Tip | Durum |
|---|-------|-----|-------|
| 01 | [Titanic](projects/01_titanic) | Tabular sınıflandırma | ⬜ |
| 02 | [Bank Dataset Binary Classification](projects/02_bank_binary_classification) | Tabular sınıflandırma | ⬜ |
| 03 | [Introverts vs Extroverts](projects/03_introverts_vs_extroverts) | Tabular sınıflandırma | ⬜ |
| 04 | [Disaster Tweets](projects/04_disaster_tweets_nlp) | NLP | ⬜ |
| 05 | [Spooky Author Identification](projects/05_spooky_author_identification) | NLP | ⬜ |
| 06 | [Turkish Text Categorization](projects/06_turkish_text_categorization) | NLP | ⬜ |
| 07 | [TREMO Emotion](projects/07_tremo_turkish_emotion) | NLP | ⬜ |
| 08 | [ChatGPT App Reviews](projects/08_chatgpt_app_reviews) | NLP | ⬜ |
| 09 | [Chat Sentiment](projects/09_chat_sentiment) | NLP | ⬜ |
| 10 | [Medical Cost](projects/10_medical_cost) | Tabular regresyon | ⬜ |
| 11 | [Audi A1 Listings](projects/11_audi_a1_listings) | Tabular regresyon | ⬜ |
| 12 | [MNIST CSV](projects/12_mnist_csv) | Görüntü | ⬜ |
| 13 | [Multilingual App Reviews](projects/13_multilingual_app_reviews) | NLP | ⬜ |
| 14 | [Emotion Detection from Text](projects/14_emotion_detection_text) | NLP | ⬜ |
| 15 | [Reddit Posts and Comments](projects/15_reddit_posts_comments) | NLP | ⬜ |
| 16 | [Amazon Fine Food Reviews](projects/16_amazon_fine_food_reviews) | NLP | ⬜ |
| 17 | [IMDB 50K Reviews](projects/17_imdb_50k_reviews) | NLP | ⬜ |
| 18 | [Emotions for NLP](projects/18_emotions_nlp) | NLP | ⬜ |
| 19 | [SMS Spam](projects/19_sms_spam) | Dengesiz sınıflandırma | ⬜ |
| 20 | [Airplane Price](projects/20_airplane_price) | Tabular regresyon | ⬜ |
| 21 | [Iris](projects/21_iris) | Tabular sınıflandırma | ⬜ |
| 22 | [Credit Card Default](projects/22_credit_card_default) | Dengesiz sınıflandırma | ⬜ |
| 23 | [Student Marks](projects/23_student_marks) | Tabular regresyon | ⬜ |
| 24 | [Car Sales](projects/24_car_sales) | Tabular regresyon | ⬜ |
| 25 | [Pokemon Stats](projects/25_pokemon_stats) | Tabular sınıflandırma | ⬜ |
| 26 | [Student Grades](projects/26_student_grades) | Tabular regresyon | ⬜ |

## Derin öğrenme

ML bölümü bittikten sonra [`dl/`](dl) altında aynı mantıkla genişleyecek.

## Kurulum

```bash
pip install -r requirements.txt
jupyter lab
```

Veri setleri repoya eklenmez; her proje README'sindeki isimle Kaggle'dan indirilir.
