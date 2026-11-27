# 05 · Banka Abonelik Tahmini (Dengesiz Sınıflandırma)

| Özellik | Değer |
|---|---|
| Seviye | Orta |
| Problem Tipi | Dengesiz Tabular Sınıflandırma (İkili) |
| Hedef Değişken | `y` (`0`: `%87.93`, `1`: `%12.07` — `750.000` satır) |
| Veri Kaynağı | [Kaggle — Playground Series S5E8](https://www.kaggle.com/competitions/playground-series-s5e8) |

**Bu projede ne öğrenirsin?**
Büyük ve dengesiz veride doğru metrik okuma ve hiperparametre arama: majority baseline tuzağı (`%87.86`), `RandomForest(class_weight='balanced')` ile `XGBClassifier(scale_pos_weight=...)` karşılaştırması, `scale_pos_weight`'i yalnızca `y_train`'den hesaplama ve F1 odaklı `GridSearchCV`.

## Rehber Eşleme Tablosu (`guide/`)

| # | Adım | Kapsam | Not |
|---|---|---|---|
| 01 | [Problem, metrik ve baseline](../../guide/01_problem_metric_baseline.md) | Kısmi | F1 ve `classification_report` kullanıldı; `DummyClassifier` kodda eksik (majority baseline aşağıda hesaplandı) |
| 02 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | Uygulandı | `info()`, `isnull().sum()`, `id` sütunu çıkarıldı |
| 03 | [EDA ve veri sızıntısı avı](../../guide/03_eda_and_leakage.md) | Atlandı | `duration` sızıntı riski aşağıda not edildi |
| 04 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | Uygulandı | %20 holdout `train_test_split` + 3-Fold `GridSearchCV` |
| 05 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | Atlandı | Eksik değer `0`; aykırı değer analizi yapılmadı |
| 06 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | Atlandı | |
| 07 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | Uygulandı | `OneHotEncoder(handle_unknown="ignore")` `Pipeline` içinde |
| 08 | [Özellik seçimi](../../guide/08_feature_selection.md) | Atlandı | |
| 09 | [Dengesiz veri ve pipeline](../../guide/09_imbalance_and_pipeline.md) | Uygulandı | `class_weight='balanced'`, `y_train`'den `scale_pos_weight=7.30`, `Pipeline` |
| 10 | [Model seçimi ve tuning](../../guide/10_modeling_and_tuning.md) | Uygulandı | `RandomForest` vs `XGBoost` + `GridSearchCV(scoring='f1')` |
| 11 | [Hata analizi ve kaydetme](../../guide/11_error_analysis_and_export.md) | Atlandı | Yanlış örnek analizi ve model kaydetme yok |

## Sonuçlar (Holdout Doğrulama — %20 `train_test_split`)

> **Not:** Tüm değerlendirme skorları yarışma `test.csv` üzerinde değil, `train.csv` (`750.000` satır) içinden ayrılan **%20 holdout doğrulama seti** (`150.000` satır: `131.795` sınıf `0`, `18.205` sınıf `1`) üzerinde ölçülmüştür.

- **Majority Baseline (hepsine `0` tahmini):** Holdout Accuracy = **%87.86** (`131.795 / 150.000`, tüm train'de `%87.93`) | **Class 1 F1 = `0.00`**
- Dengesiz veride Accuracy yanıltıcı olduğundan ana metrik **Class 1 F1 (`0.67`)** ve **3-Fold CV F1 (`0.6668`)** skorlarıdır.

| Model | **Class 1 F1 (Ana Metrik)** | Class 1 Precision / Recall | Holdout Accuracy | Yöntem |
|---|---|---|---|---|
| Majority Baseline | **`0.00`** | `0.00` / `0.00` | `%87.86` | Her gözleme `0` tahmini |
| RandomForest | Hesaplanmadı | Hesaplanmadı | `%84.22` (`0.8422`) | `class_weight='balanced'` |
| XGBoost | **`0.67`** | `0.52` / `0.93` | `%88.70` (`0.8870`) | `scale_pos_weight = 7.30` (`y_train`'den) |
| XGBoost + GridSearchCV | **`0.67`** *(3-Fold CV F1: **`0.6668`**)* | `0.52` / `0.93` | `%88.70` (`0.8870`) | `learning_rate=0.1, max_depth=7, n_estimators=200` |

## Bilinen Sorunlar ve Eksikler
- **`duration` Leakage Riski:** Veri setindeki `duration` (son telefon görüşmesinin süresi) sütunu model girdisinde kullanılmaktadır. Gerçek dünya senaryosunda arama süresi yalnızca görüşme bittikten sonra bilindiği için **tahmin anında (arama öncesinde) bilinemez** ve canlı sistemde *look-ahead / target leakage* oluşturur. Bu çalışmada Kaggle yarışma şemasına sadık kalınarak benchmark amacıyla korunmuştur; üretime alınacak bir modelde `duration` çıkarılmalıdır.
- `ROC-AUC`, `PR-AUC` ve karar eşiği optimizasyonu (threshold tuning) yapılmamıştır; RandomForest için `classification_report` hesaplanmamıştır.

## Veri ve Çalıştırma

```bash
pip install -r ../../requirements.txt
kaggle competitions download -c playground-series-s5e8
jupyter lab notebook.ipynb
```

Notebook Kaggle ortamında doğrudan çalışır. Yerelde çalıştırmak için dosyaları `data/` klasörüne koyup dosya yollarını uyarlayabilirsiniz.

---
[← Önceki Proje: 04 · Introverts vs Extroverts](../04_introverts_vs_extroverts/README.md) · [Sonraki Proje: 06 · Disaster Tweets NLP →](../06_disaster_tweets_nlp/README.md)
