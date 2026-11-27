# 03 · Titanic Hayatta Kalma Tahmini

| Özellik | Değer |
|---|---|
| Seviye | Başlangıç |
| Problem Tipi | Tabular Sınıflandırma (İkili) |
| Hedef Değişken | `Survived` (`0` = Hayır, `1` = Evet — `891` satır) |
| Veri Kaynağı | [Kaggle — Titanic](https://www.kaggle.com/c/titanic) |

**Bu projede ne öğrenirsin?**
Regresyondan sınıflandırmaya ve **Scikit-learn `Pipeline` mimarisine** geçiş: kimlik sütunu (`PassengerId`) ayıklama, `ColumnTransformer` ile sayısal (`median`) ve kategorik (`most_frequent` + `OneHotEncoder`) sütunları tek boru hattında birleştirme, `XGBClassifier` ve `classification_report` yorumlama.

## Rehber Eşleme Tablosu (`guide/`)

| # | Adım | Kapsam | Not |
|---|---|---|---|
| 01 | [Problem, metrik ve baseline](../../guide/01_problem_metric_baseline.md) | Kısmi | Accuracy ve `classification_report` hesaplandı; `DummyClassifier` baseline eksik |
| 02 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | Uygulandı | `info()`, `PassengerId` ve `Name` çıkarıldı |
| 03 | [EDA ve veri sızıntısı avı](../../guide/03_eda_and_leakage.md) | Atlandı | |
| 04 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | Kısmi | %20 holdout `train_test_split` uygulandı; Cross-Validation eksik |
| 05 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | Uygulandı | `SimpleImputer` (sayısal `median`, kategorik `most_frequent`) `Pipeline` içinde |
| 06 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | Atlandı | |
| 07 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | Uygulandı | `OneHotEncoder(handle_unknown='ignore')` `Pipeline` içinde |
| 08 | [Özellik seçimi](../../guide/08_feature_selection.md) | Atlandı | |
| 09 | [Dengesiz veri ve pipeline](../../guide/09_imbalance_and_pipeline.md) | Uygulandı | `ColumnTransformer` + `Pipeline` + `XGBClassifier` |
| 10 | [Model seçimi ve tuning](../../guide/10_modeling_and_tuning.md) | Kısmi | Tek model (`XGBClassifier`), tuning yok |
| 11 | [Hata analizi ve kaydetme](../../guide/11_error_analysis_and_export.md) | Atlandı | Hata analizi ve model kaydetme yok |

## Sonuçlar (Holdout Doğrulama — %20 `train_test_split`)

> **Not:** Skorlar yarışma `test.csv` üzerinde değil, `train.csv` (`891` satır) içinden `train_test_split(..., test_size=0.2, random_state=42)` ile ayrılan **%20 holdout doğrulama seti** (`179` yolcu) üzerinde ölçülmüştür.

| Model | Holdout Accuracy | Macro F1 | Class 0 F1 | Class 1 F1 |
|---|---|---|---|---|
| XGBoost + Pipeline | **%79.33** (`0.7933`) | **0.79** | `0.82` | `0.75` |

## Bilinen Sorunlar ve Eksikler
- `Cross-Validation` yerine tek bir holdout ayrımı kullanılmıştır.
- `DummyClassifier` baseline, `Name`/`Cabin`/`Ticket` üzerinde özellik mühendisliği ve hata analizi yapılmamıştır (`Ticket` ve `Cabin` ham olarak one-hot kodlanmaktadır).

## Veri ve Çalıştırma

```bash
pip install -r ../../requirements.txt
kaggle competitions download -c titanic
jupyter lab notebook.ipynb
```

Notebook Kaggle ortamında doğrudan çalışır. Yerelde çalıştırmak için `train.csv` ve `test.csv` dosyalarını `data/` klasörüne koyup dosya yollarını uyarlayabilirsiniz.

---
[← Önceki Proje: 02 · Uçak Fiyat Tahmini](../02_airplane_price_regression/README.md) · [Sonraki Proje: 04 · Introverts vs Extroverts →](../04_introverts_vs_extroverts/README.md)
