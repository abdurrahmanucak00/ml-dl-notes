# 04 · Introverts vs Extroverts

| Özellik | Değer |
|---|---|
| Seviye | Orta |
| Problem Tipi | Tabular Sınıflandırma (İkili) |
| Hedef Değişken | `Personality` (`Extrovert` / `Introvert`) |
| Veri Kaynağı | [Kaggle — Playground Series S5E7](https://www.kaggle.com/competitions/playground-series-s5e7) |

**Bu projede ne öğrenirsin?**
Tek holdout ayrımından **5-Fold Cross-Validation**'a geçiş ve kapsamlı sınıflandırma değerlendirmesi: `Pipeline` + `ColumnTransformer` içinde eksik değer doldurma, string etiketlerde `make_scorer(f1_score, pos_label='Extrovert')` kullanımı, `F1` ve `ROC-AUC` metriklerini birlikte okuma.

## Rehber Eşleme Tablosu (`guide/`)

| # | Adım | Kapsam | Not |
|---|---|---|---|
| 01 | [Problem, metrik ve baseline](../../guide/01_problem_metric_baseline.md) | Kısmi | Accuracy, F1, ROC-AUC hesaplandı; `DummyClassifier` baseline eksik |
| 02 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | Uygulandı | `id` çıkarıldı, eksik değer ve kategorik benzersiz değer kontrolü yapıldı |
| 03 | [EDA ve veri sızıntısı avı](../../guide/03_eda_and_leakage.md) | Atlandı | |
| 04 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | Uygulandı | %20 holdout `train_test_split` + 5-Fold CV (`cross_val_score`) |
| 05 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | Uygulandı | `SimpleImputer` (sayısal `median`, kategorik `most_frequent`) `Pipeline` içinde |
| 06 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | Atlandı | |
| 07 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | Uygulandı | `OneHotEncoder(handle_unknown="ignore")` `Pipeline` içinde |
| 08 | [Özellik seçimi](../../guide/08_feature_selection.md) | Atlandı | |
| 09 | [Dengesiz veri ve pipeline](../../guide/09_imbalance_and_pipeline.md) | Uygulandı | `ColumnTransformer` + `Pipeline` + `RandomForestClassifier` |
| 10 | [Model seçimi ve tuning](../../guide/10_modeling_and_tuning.md) | Kısmi | Tek model (`RandomForestClassifier`), hiperparametre araması yok |
| 11 | [Hata analizi ve kaydetme](../../guide/11_error_analysis_and_export.md) | Atlandı | Yanlış örnek analizi ve model kaydetme yok |

## Sonuçlar (Holdout Doğrulama — %20 `train_test_split` + 5-Fold CV)

> **Not:** `Holdout` skorları yarışma `test.csv` üzerinde değil, `train.csv` içinden `train_test_split(..., test_size=0.2, random_state=42)` ile ayrılan **%20 holdout doğrulama seti** (`3.705` gözlem) üzerinde ölçülmüştür.

| Model | Train Accuracy | Holdout Accuracy | 5-Fold CV F1 | Holdout F1 | Holdout ROC-AUC |
|---|---|---|---|---|---|
| RandomForest + Pipeline | **%96.92** (`0.9692`) | **%96.87** (`0.9687`) | **0.9787 ± 0.0023** | **0.9790** | **0.9649** |

## Bilinen Sorunlar ve Eksikler
- `RandomForestClassifier(n_estimators=150, max_depth=4)` içinde **`random_state` sabitlenmemiştir**; bu nedenle her çalıştırmada ağaç rastgeleliğine bağlı küçük skor farklılıkları oluşabilir.
- Alternatif model karşılaştırması, hiperparametre optimizasyonu ve hata analizi yapılmamıştır.

## Veri ve Çalıştırma

```bash
pip install -r ../../requirements.txt
kaggle competitions download -c playground-series-s5e7
jupyter lab notebook.ipynb
```

Notebook Kaggle ortamında doğrudan çalışır. Yerelde çalıştırmak için dosyaları `data/` klasörüne koyup dosya yollarını uyarlayabilirsiniz.

---
[← Önceki Proje: 03 · Titanic](../03_titanic_survival/README.md) · [Sonraki Proje: 05 · Banka Abonelik Tahmini →](../05_bank_imbalanced_classification/README.md)
