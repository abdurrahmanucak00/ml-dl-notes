# Titanic - Machine Learning from Disaster

| | |
|---|---|
| Kaynak | [Kaggle - Titanic](https://www.kaggle.com/c/titanic) |
| Tip | Tabular sınıflandırma (ikili) |
| Hedef | `Survived` (`0` = Hayır, `1` = Evet) |
| Durum | ✅ tamamlandı |

**Bu projede ne gösteriliyor:** `ColumnTransformer` + `Pipeline` ile sayısal/kategorik sütunları ayrı ön işleyip `XGBClassifier` ile hayatta kalma tahmini; kimlik sütunu (`PassengerId`) temizliği ve ayrılan holdout seti üzerinde değerlendirme.

## Checklist'in hangi adımları kullanıldı

| # | Adım | Durum | Not |
|---|------|-------|-----|
| 1 | [Problem, metrik, baseline](../../guide/01_problem_metric_baseline.md) | ➖ kısmi / eksik | Accuracy ve `classification_report` hesaplandı; `DummyClassifier` baseline eksik |
| 2 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | ✅ uygulandı | `info()`, `PassengerId` ve `Name` çıkarıldı |
| 3 | [EDA ve sızıntı avı](../../guide/03_eda_and_leakage.md) | ➖ atlandı / eksik | |
| 4 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | ➖ kısmi / eksik | %20 `train_test_split` uygulandı; Cross-Validation eksik |
| 5 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | ✅ uygulandı | `SimpleImputer` (sayısal `median`, kategorik `most_frequent`) `Pipeline` içinde |
| 6 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | ➖ atlandı / eksik | |
| 7 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | ✅ uygulandı | `OneHotEncoder(handle_unknown='ignore')` `Pipeline` içinde |
| 8 | [Özellik seçimi](../../guide/08_feature_selection.md) | ➖ atlandı / eksik | |
| 9 | [Dengesizlik ve pipeline](../../guide/09_imbalance_and_pipeline.md) | ✅ uygulandı | `ColumnTransformer` + `Pipeline` + `XGBClassifier` |

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

Notebook Kaggle ortamında doğrudan çalışır (`/kaggle/input/...` dinamik yolu ile). Yerelde çalıştırmak için `train.csv` ve `test.csv` dosyalarını `data/` klasörüne koyup dosya yollarını uyarlayabilirsiniz.
