# 08 · ChatGPT Çok Dilli Yorumlar (Duygu Analizi)

| Özellik | Değer |
|---|---|
| Seviye | İleri |
| Problem Tipi | NLP — Çok Dilli İkili Duygu Sınıflandırma |
| Hedef Değişken | `sentiment` (`rating >= 3` → `positive`, `rating < 3` → `negative`) |
| Veri Kaynağı | [Kaggle — saloni1712/chatgpt-app-reviews](https://www.kaggle.com/datasets/saloni1712/chatgpt-app-reviews) |

**Bu projede ne öğrenirsin?**
Gürültülü gerçek dünya metinleriyle çalışma: `langdetect` ile dil dağılımı tespiti, `deep_translator` ile toplu çeviri (`translate_batch`), yıldız puanından etiket türetme, sızıntısız TF-IDF ve dengesiz sınıfta majority baseline karşısında `Negative Recall` analizi.

## Rehber Eşleme Tablosu (`guide/`)

| # | Adım | Kapsam | Not |
|---|---|---|---|
| 01 | [Problem, metrik ve baseline](../../guide/01_problem_metric_baseline.md) | Kısmi | Accuracy, Macro F1, `classification_report` var; `DummyClassifier` kodda eksik (majority baseline aşağıda hesaplandı) |
| 02 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | Uygulandı | Tekrar eden yorumlar çıkarıldı, çeviri + noktalama/rakam/stopword/lemmatization |
| 03 | [EDA ve veri sızıntısı avı](../../guide/03_eda_and_leakage.md) | Uygulandı | Sınıf dağılımı, dil dağılımı, WordCloud; TF-IDF leakage düzeltildi |
| 04 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | Kısmi | %20 holdout `train_test_split` uygulandı; Cross-Validation eksik |
| 05 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | Atlandı | Eksik değer `0` |
| 06 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | Atlandı | |
| 07 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | Uygulandı | `TfidfVectorizer(max_features=1000, ngram_range=(1,2))` yalnızca `x_train` üzerine `fit_transform` |
| 08 | [Özellik seçimi](../../guide/08_feature_selection.md) | Atlandı | |
| 09 | [Dengesiz veri ve pipeline](../../guide/09_imbalance_and_pipeline.md) | Atlandı | Sınıf dengesizliği (`%72` / `%28`) için `class_weight`/eşik ayarı veya `Pipeline` yok |
| 10 | [Model seçimi ve tuning](../../guide/10_modeling_and_tuning.md) | Kısmi | Tek model (`MultinomialNB`), tuning yok |
| 11 | [Hata analizi ve kaydetme](../../guide/11_error_analysis_and_export.md) | Kısmi | Confusion Matrix çizildi; yanlış örnek analizi ve kaydetme yok |

## Sonuçlar (Holdout Doğrulama — %20 `train_test_split`)

> **Not:** Değerlendirme metrikleri `train_test_split(..., test_size=0.2, random_state=42)` ile ayrılan **%20 holdout doğrulama seti** (`452` yorum: `322` `positive`, `130` `negative`) üzerinde hesaplanmıştır.

- **Majority Baseline (hepsine `positive` tahmini):** Holdout Accuracy = **%71.24** (`322 / 452`, tüm `2.257` tekil yorumda `%72.04`) | **Negative Recall = `0.00`**

| Model | Holdout Accuracy | F1 (macro) | **Negative Recall** | Negative Precision / F1 | Positive Precision / Recall / F1 |
|---|---|---|---|---|---|
| Majority Baseline | `%71.24` | `0.42` | **`0.00`** | `0.00` / `0.00` | `0.71` / `1.00` / `0.83` |
| MultinomialNB (TF-IDF, düzeltilmiş) | **%79.42** (`0.7942`) | **0.6937** | **`0.38`** | `0.79` / `0.52` | `0.79` / `0.96` / `0.87` |

## Bilinen Sorunlar ve Eksikler
- **Çeviri Adımı Sınırlaması (Doğrulanamayan Bilgi):** İngilizce dışındaki `~352` yorum `GoogleTranslator.translate_batch` ile 25'li gruplar halinde çevrilir. Bir batch hata alırsa (`except Exception: pass`) ilgili satırların orijinal ham metni sessizce korunur (satır sayısı ve sırası bozulmaz). Çalıştırma çıktısında/logunda kaç batch'in başarıyla çevrildiği veya hata aldığı yazdırılmadığı için fiili çeviri başarı oranı **doğrulanamamaktadır**.
- `negative` sınıfı için recall (`0.38`) düşüktür; sınıf dengesizliği yönetimi, Cross-Validation ve alternatif model karşılaştırması eksiktir.

## Veri ve Çalıştırma

```bash
pip install -r ../../requirements.txt
kaggle datasets download -d saloni1712/chatgpt-app-reviews
jupyter lab notebook.ipynb
```

Notebook Kaggle ortamında doğrudan çalışır. Yerelde çalıştırmak için `chatgpt_reviews.csv` dosyasını `data/` klasörüne koyup dosya yolunu uyarlayabilirsiniz.

---
[← Önceki Proje: 07 · Spooky Author NLP](../07_spooky_author_nlp/README.md) · [Ana Sayfa →](../../README.md)
