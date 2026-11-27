# ml-dl-notes

Makine öğrenmesine yeni başlayanlar için **ham veriden değerlendirilmiş modele** kadar tüm süreci uygulamalı anlatan, veri sızıntısı (data leakage) odaklı Türkçe müfredat ve proje koleksiyonu.

## Nasıl Kullanılır?

1. **Kavramları Öğren (`guide/`):** [00 · Ana Kontrol Listesi](guide/00_data_to_model_checklist.md) ile süreci haritala, `01`–`11` rehberleriyle her adımın mantığını ve kod kalıplarını incele.
2. **Projeleri Sırayla Takip Et (`projects/`):** Temel regresyondan çok dilli NLP'ye kadar kolaydan zora sıralanmış, Kaggle üzerinde doğrulanmış çıktılı notebook'ları takip et.
3. **Kendi Verine Uygula:** Her proje klasöründeki rehber eşleme tablosunu ve "Bilinen Sorunlar" notlarını kendi çalışmalarında referans al.

---

## Rehber Müfredatı (`guide/`)

| # | Konu | Ne Öğretir? |
|---|------|-------------|
| 00 | [Veriden Modele Harita](guide/00_data_to_model_checklist.md) | Tüm sürecin tek bakışta akışı ve temel kurallar |
| 01 | [Problem, Metrik ve Baseline](guide/01_problem_metric_baseline.md) | Doğru metrik seçimi ve referans model (`Dummy`) kurma |
| 02 | [İlk Temas ve Temizlik](guide/02_first_look_and_cleaning.md) | Veri tipleri, bellek, duplicate ve kimlik sütunları |
| 03 | [EDA ve Veri Sızıntısı Avı](guide/03_eda_and_leakage.md) | Dağılımlar, korelasyonlar ve 4 temel leakage türü |
| 04 | [Bölme ve Doğrulama](guide/04_splitting_and_validation.md) | Holdout, KFold, StratifiedKFold ve CV güvenilirliği |
| 05 | [Eksik ve Aykırı Değer](guide/05_missing_values_and_outliers.md) | Eksiklik mekanizmaları, imputation ve uç değer kararları |
| 06 | [Özellik Mühendisliği](guide/06_feature_engineering.md) | Oranlar, grup istatistikleri, zaman ve metin özellikleri |
| 07 | [Encoding, Scaling ve Vektörleme](guide/07_encoding_and_scaling.md) | Kategorik kodlama, ölçeklendirme ve TF-IDF |
| 08 | [Özellik Seçimi](guide/08_feature_selection.md) | Gürültü eleme, Permutation Importance ve SHAP |
| 09 | [Dengesiz Veri ve Pipeline](guide/09_imbalance_and_pipeline.md) | `class_weight`, eşik seçimi, `ColumnTransformer` + `Pipeline` |
| 10 | [Model Seçimi ve Tuning](guide/10_modeling_and_tuning.md) | Algoritma seçimi, overfitting teşhisi ve `GridSearchCV` |
| 11 | [Hata Analizi ve Model Kaydetme](guide/11_error_analysis_and_export.md) | Confusion Matrix, artık analizi ve `joblib` ile kaydetme |

---

## Uygulamalı Projeler (`projects/`)

Projeler **kolaydan zora** doğru sıralanmıştır; her çalışma bir öncekinin üzerine yeni bir beceri ekler.

| # | Proje | Problem Tipi | Odaklanılan Teknikler | Seviye |
|---|-------|--------------|-----------------------|--------|
| 01 | [Audi A1 Fiyat Tahmini](projects/01_audi_a1_price_regression) | Regresyon | Birim temizleme, sızıntı sütunu eleme, `LinearRegression`, `R²/RMSE/MAE` | Başlangıç |
| 02 | [Uçak Fiyat Tahmini](projects/02_airplane_price_regression) | Regresyon | Split sonrası imputation & encoding, `StandardScaler`, yaygın ön işleme hatalarından kaçınma | Başlangıç |
| 03 | [Titanic Hayatta Kalma](projects/03_titanic_survival) | İkili Sınıflandırma | `ColumnTransformer`, `Pipeline`, kimlik sütunu temizliği, `XGBClassifier` | Başlangıç |
| 04 | [Introverts vs Extroverts](projects/04_introverts_vs_extroverts) | İkili Sınıflandırma | `RandomForest` Pipeline, 5-Fold CV, `F1` ve `ROC-AUC` | Orta |
| 05 | [Banka Abonelik Tahmini](projects/05_bank_imbalanced_classification) | Dengesiz Sınıflandırma | Majority baseline, `scale_pos_weight`, F1 odaklı `GridSearchCV`, `duration` analizi | Orta |
| 06 | [Disaster Tweets](projects/06_disaster_tweets_nlp) | NLP (İkili) | Regex sırası, Pipeline içinde `TfidfVectorizer`, NB vs. XGBoost | Orta |
| 07 | [Spooky Author Identification](projects/07_spooky_author_nlp) | NLP (Çok Sınıflı) | Metin EDA & WordCloud, `stratify=y`, 5-Fold CV, özellik yorumlama | Orta–İleri |
| 08 | [ChatGPT Çok Dilli Yorumlar](projects/08_chatgpt_multilingual_sentiment) | NLP (Çok Dilli) | `langdetect`, toplu çeviri, `Negative Recall` ve baseline kıyası | İleri |

---

## Yol Haritası (Roadmap)

**Faz 2 — Çekirdek ML Serisinin Genişletilmesi**
- **Medical Cost (Regresyon):** Çarpık hedefte `log1p` dönüşümü, özellik etkileşimleri ve artık (residual) analizi
- **Credit Card Default / SMS Spam (Dengesiz Sınıflandırma):** `PR-AUC` ve karar eşiği optimizasyonu (threshold tuning)
- **Ames Housing (İleri Regresyon Capstone):** Yapısal eksik değerler, alan bilgisiyle Feature Engineering ve Ensemble
- **TREMO Türkçe Duygu Analizi (Türkçe NLP / DL Köprüsü):** TF-IDF baseline vs. Türkçe BERT fine-tuning

**Faz 3 — Derin Öğrenme ([`dl/`](dl))**
- Eğitim döngüsü rehberi (augmentation, learning rate schedule, early stopping), görüntü sınıflandırma (`MLP` → `CNN`) ve Transformer temelleri

---

## Kurulum

```bash
git clone https://github.com/abdurrahmanucak00/ml-dl-notes.git
cd ml-dl-notes
pip install -r requirements.txt
jupyter lab
```

> Veri setleri repoya eklenmez; her proje klasöründeki `README.md` dosyasında ilgili Kaggle veri kaynağı ve indirme komutu verilmiştir. Notebook'lar Kaggle ortamında doğrudan çalışır; yerelde çalıştırmak için CSV dosyalarını ilgili projenin `data/` klasörüne indirip dosya yolunu uyarlayabilirsiniz.
