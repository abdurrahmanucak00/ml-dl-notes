# ChatGPT App Reviews — Sentiment Analysis

| | |
|---|---|
| Kaynak | [Kaggle - saloni1712/chatgpt-app-reviews](https://www.kaggle.com/datasets/saloni1712/chatgpt-app-reviews) |
| Tip | NLP / çok dilli metin (ikili duygu sınıflandırma) |
| Hedef | `sentiment` (`rating >= 3` → `positive`, `rating < 3` → `negative`) |
| Durum | ✅ tamamlandı |

**Bu projede ne gösteriliyor:** Çok dilli yorumlarda `langdetect` ile dil tespiti, `deep_translator` ile toplu çeviri, metin temizleme, sızıntısız `TfidfVectorizer` (`train_test_split` sonrası `fit_transform`) ve `MultinomialNB` değerlendirmesi.

## Checklist'in hangi adımları kullanıldı

| # | Adım | Durum | Not |
|---|------|-------|-----|
| 1 | [Problem, metrik, baseline](../../guide/01_problem_metric_baseline.md) | ➖ kısmi / eksik | Accuracy, Macro F1, `classification_report` var; `DummyClassifier` kodda eksik (majority baseline aşağıda hesaplandı) |
| 2 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | ✅ uygulandı | Tekrar eden yorumlar çıkarıldı, çeviri + noktalama/rakam/stopword/lemmatization |
| 3 | [EDA ve sızıntı avı](../../guide/03_eda_and_leakage.md) | ✅ uygulandı | Sınıf dağılımı, dil dağılımı, WordCloud; TF-IDF leakage düzeltildi |
| 4 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | ➖ kısmi / eksik | %20 holdout `train_test_split` uygulandı; Cross-Validation eksik |
| 5 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | ➖ atlandı / eksik | Eksik değer `0` |
| 6 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | ➖ atlandı / eksik | |
| 7 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | ✅ uygulandı | `TfidfVectorizer(max_features=1000, ngram_range=(1,2))` yalnızca `x_train` üzerine `fit_transform` |
| 8 | [Özellik seçimi](../../guide/08_feature_selection.md) | ➖ atlandı / eksik | |
| 9 | [Dengesizlik ve pipeline](../../guide/09_imbalance_and_pipeline.md) | ➖ atlandı / eksik | Sınıf dengesizliği (`%72` / `%28`) için `class_weight`/eşik ayarı veya `Pipeline` yok |

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
