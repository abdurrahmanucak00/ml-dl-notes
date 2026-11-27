# 01 · Audi A1 Fiyat Tahmini

| Özellik | Değer |
|---|---|
| Seviye | Başlangıç |
| Problem Tipi | Tabular Regresyon |
| Hedef Değişken | `Price(£)` (`471` satır) |
| Veri Kaynağı | [Kaggle — jacklacey/audi-a1-listings](https://www.kaggle.com/datasets/jacklacey/audi-a1-listings) |

**Bu projede ne öğrenirsin?**
İlk uçtan uca regresyon akışı: hedeften türetilmiş veri sızıntısı sütunlarını (`PriceRank`, `PPY`, `PPYRank`, `Score`, `MileageRank`) tespit edip çıkarma, `"1.6L"` gibi birim eklerini temizleyip sayısala çevirme ve `LinearRegression` modelini `R²`, `RMSE`, `MAE` ile değerlendirme.

## Rehber Eşleme Tablosu (`guide/`)

| # | Adım | Kapsam | Not |
|---|---|---|---|
| 01 | [Problem, metrik ve baseline](../../guide/01_problem_metric_baseline.md) | Kısmi | `R²`, `RMSE`, `MAE` hesaplandı; `DummyRegressor` baseline eksik |
| 02 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | Uygulandı | `index`/`href` çıkarıldı, `Engine` sütunundan `"L"` temizlenip sayısala çevrildi |
| 03 | [EDA ve veri sızıntısı avı](../../guide/03_eda_and_leakage.md) | Uygulandı | `PriceRank`, `PPY`, `PPYRank`, `Score`, `MileageRank` sızıntı sütunları çıkarıldı |
| 04 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | Kısmi | %20 holdout `train_test_split` uygulandı; Cross-Validation eksik |
| 05 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | Atlandı | Eksik değer `0`; aykırı değer analizi yapılmadı |
| 06 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | Atlandı | |
| 07 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | Kısmi | `pd.get_dummies` uygulandı (aşağıdaki bilinen soruna bakınız); scaling yok |
| 08 | [Özellik seçimi](../../guide/08_feature_selection.md) | Atlandı | |
| 09 | [Dengesiz veri ve pipeline](../../guide/09_imbalance_and_pipeline.md) | Atlandı | `Pipeline` kullanılmadı |
| 10 | [Model seçimi ve tuning](../../guide/10_modeling_and_tuning.md) | Kısmi | Tek model (`LinearRegression`) kuruldu |
| 11 | [Hata analizi ve kaydetme](../../guide/11_error_analysis_and_export.md) | Atlandı | Artık (residual) analizi ve model kaydetme yok |

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

---
[← Ana Sayfa](../../README.md) · [Sonraki Proje: 02 · Uçak Fiyat Tahmini →](../02_airplane_price_regression/README.md)
