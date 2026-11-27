# Audi A1 Listings — Price Prediction

| | |
|---|---|
| Kaynak | [Kaggle - jacklacey/audi-a1-listings](https://www.kaggle.com/datasets/jacklacey/audi-a1-listings) |
| Tip | Tabular regresyon |
| Hedef | `Price(£)` (`471` satır) |
| Durum | ✅ tamamlandı |

**Bu projede ne gösteriliyor:** Hedeften türetilmiş veri sızıntısı sütunlarının (`PriceRank`, `PPY`, `PPYRank`, `Score`, `MileageRank`) çıkarılması, `"1.6L"` gibi birim eklerinin sayısala çevrilmesi ve `LinearRegression` modelinin `R²`, `RMSE`, `MAE` ile değerlendirilmesi.

## Checklist'in hangi adımları kullanıldı

| # | Adım | Durum | Not |
|---|------|-------|-----|
| 1 | [Problem, metrik, baseline](../../guide/01_problem_metric_baseline.md) | ➖ kısmi / eksik | `R²`, `RMSE`, `MAE` hesaplandı; `DummyRegressor` baseline eksik |
| 2 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | ✅ uygulandı | `index`/`href` çıkarıldı, `Engine` sütunundan `"L"` temizlenip sayısala çevrildi |
| 3 | [EDA ve sızıntı avı](../../guide/03_eda_and_leakage.md) | ✅ uygulandı | `PriceRank`, `PPY`, `PPYRank`, `Score`, `MileageRank` sızıntı sütunları çıkarıldı |
| 4 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | ➖ kısmi / eksik | %20 holdout `train_test_split` uygulandı; Cross-Validation eksik |
| 5 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | ➖ atlandı / eksik | Eksik değer `0`; aykırı değer analizi yapılmadı |
| 6 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | ➖ atlandı / eksik | |
| 7 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | ➖ kısmi / eksik | `pd.get_dummies` uygulandı (aşağıdaki bilinen soruna bakınız); scaling yok |
| 8 | [Özellik seçimi](../../guide/08_feature_selection.md) | ➖ atlandı / eksik | |
| 9 | [Dengesizlik ve pipeline](../../guide/09_imbalance_and_pipeline.md) | ➖ atlandı / eksik | `Pipeline` kullanılmadı |

## Sonuçlar (Holdout Doğrulama — %20 `train_test_split`)

> **Not:** Tüm metrikler `train_test_split(..., test_size=0.2, random_state=42)` ile ayrılan **%20 holdout doğrulama seti** üzerinde hesaplanmıştır.

| Model | Holdout R² | RMSE | MAE |
|---|---|---|---|
| LinearRegression | **0.9016** | **1,366.36** | **1,140.62** |

## Bilinen Sorunlar ve Eksikler
- **`train_test_split` Öncesi `pd.get_dummies`:** Kategorik kodlama (`Type`, `Transmission`, `Fuel`) veri bölünmeden önce tüm `df` üzerinde yapılmıştır. Doğru mühendislik pratiği `Pipeline` + `OneHotEncoder(handle_unknown="ignore")` ile yalnızca `x_train` üzerinde fit etmektir.
- Cross-Validation, artık (residual) analizi ve alternatif regresyon modelleri eksiktir.

## Veri ve Çalıştırma

```bash
pip install -r ../../requirements.txt
kaggle datasets download -d jacklacey/audi-a1-listings
jupyter lab notebook.ipynb
```

Notebook Kaggle ortamında doğrudan çalışır. Yerelde çalıştırmak için `Audi_A1_listings.csv` dosyasını `data/` klasörüne koyup dosya yolunu uyarlayabilirsiniz.
