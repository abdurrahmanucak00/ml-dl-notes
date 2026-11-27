# Introverts vs Extroverts

| | |
|---|---|
| Kaynak | [Kaggle - Playground Series S5E7](https://www.kaggle.com/competitions/playground-series-s5e7) |
| Tip | Tabular sınıflandırma (ikili) |
| Hedef | `Personality` (`Extrovert` / `Introvert`) |
| Durum | ✅ tamamlandı |

**Bu projede ne gösteriliyor:** Eksik değerler içeren sayısal ve kategorik özellikler için `ColumnTransformer` + `Pipeline`, `RandomForestClassifier`, 5-Fold CV F1, Holdout F1 ve `ROC-AUC` değerlendirmesi.

## Checklist'in hangi adımları kullanıldı

| # | Adım | Durum | Not |
|---|------|-------|-----|
| 1 | [Problem, metrik, baseline](../../guide/01_problem_metric_baseline.md) | ➖ kısmi / eksik | Accuracy, F1, ROC-AUC hesaplandı; `DummyClassifier` baseline eksik |
| 2 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | ✅ uygulandı | `id` çıkarıldı, eksik değer ve kategorik benzersiz değer kontrolü yapıldı |
| 3 | [EDA ve sızıntı avı](../../guide/03_eda_and_leakage.md) | ➖ atlandı / eksik | |
| 4 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | ✅ uygulandı | %20 holdout `train_test_split` + 5-Fold CV (`cross_val_score`) |
| 5 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | ✅ uygulandı | `SimpleImputer` (sayısal `median`, kategorik `most_frequent`) `Pipeline` içinde |
| 6 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | ➖ atlandı / eksik | |
| 7 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | ✅ uygulandı | `OneHotEncoder(handle_unknown="ignore")` `Pipeline` içinde |
| 8 | [Özellik seçimi](../../guide/08_feature_selection.md) | ➖ atlandı / eksik | |
| 9 | [Dengesizlik ve pipeline](../../guide/09_imbalance_and_pipeline.md) | ✅ uygulandı | `ColumnTransformer` + `Pipeline` + `RandomForestClassifier` |

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
