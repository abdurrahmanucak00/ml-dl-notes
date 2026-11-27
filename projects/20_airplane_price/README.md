# Airplane Price Prediction

| | |
|---|---|
| Kaynak | [Kaggle - rafsunahmad/plane-price-prediction](https://www.kaggle.com/datasets/rafsunahmad/plane-price-prediction) |
| Tip | Tabular regresyon |
| Hedef | `Price` (`517` satır, hedefi dolu `507` satır) |
| Durum | ✅ tamamlandı |

**Bu projede ne gösteriliyor:** Dağılım incelemesi (`histplot`), `Rcmnd cruise Knots` `.median()` parantez hatasının düzeltilmesi, `train_test_split` sonrası yalnızca `x_train`'den öğrenilen imputation/encoding/scaling ve `LinearRegression` değerlendirmesi (`R²`, `RMSE`, `MAE`).

## Checklist'in hangi adımları kullanıldı

| # | Adım | Durum | Not |
|---|------|-------|-----|
| 1 | [Problem, metrik, baseline](../../guide/01_problem_metric_baseline.md) | ➖ kısmi / eksik | `R²`, `RMSE`, `MAE` hesaplandı; `DummyRegressor` baseline eksik |
| 2 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | ➖ kısmi / eksik | Hedefi eksik satırlar atıldı; sayısal maskeli metin sütunları temizlenmedi (aşağıya bakınız) |
| 3 | [EDA ve sızıntı avı](../../guide/03_eda_and_leakage.md) | ✅ uygulandı | Eksik sütunlar için `histplot` incelendi; split öncesi imputation/encoding sızıntısı giderildi |
| 4 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | ➖ kısmi / eksik | %20 holdout `train_test_split` uygulandı; Cross-Validation eksik |
| 5 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | ✅ uygulandı | `x_train` medyan/modu ile sızıntısız doldurma; aykırı değer müdahalesi yok |
| 6 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | ➖ atlandı / eksik | |
| 7 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | ✅ uygulandı | `x_train`/`x_test` ayrı `get_dummies` + `reindex` ve `StandardScaler` |
| 8 | [Özellik seçimi](../../guide/08_feature_selection.md) | ➖ atlandı / eksik | |
| 9 | [Dengesizlik ve pipeline](../../guide/09_imbalance_and_pipeline.md) | ➖ atlandı / eksik | `sklearn.pipeline.Pipeline` kullanılmadı |

## Sonuçlar (Holdout Doğrulama — %20 `train_test_split`)

> **Not:** Tüm metrikler `train_test_split(..., test_size=0.2, random_state=42)` ile ayrılan **%20 holdout doğrulama seti** üzerinde hesaplanmıştır.

| Model | Holdout R² | RMSE | MAE |
|---|---|---|---|
| LinearRegression (düzeltilmiş) | **0.7257** | **505,871.19** | **358,087.70** |

> **Skor Değişimi Notu (`0.8036` → `0.7257`):** Orijinal notebook'taki `0.8036` R² skorundan `0.7257`'ye değişim tek başına veri sızıntısının giderilmesine bağlanamaz; split sonrası imputation/`get_dummies` (`reindex`), `Rcmnd cruise Knots` `.median()` bug düzeltmesi ve `StandardScaler` üzerinde eğitim birlikte uygulanmıştır (izole ablasyon yapılmamıştır).

## Bilinen Sorunlar ve Eksikler
- **Sayıya Çevrilmeyen `object` Sütunlar:** Sayısal özellik taşımasına rağmen metin (`object`) olarak okunan `HP or lbs thr ea engine`, `Max speed Knots`, `All eng rate of climb`, `Landing over 50ft`, `Empty weight lbs`, `Length ft/in`, `Wing span ft/in`, `Range N.M.` sütunları temizlenip sayısala çevrilmeden `pd.get_dummies` ile kategorik olarak kodlanmıştır.
- **`Model Name` Kimlik Sütunu:** Yüksek kardinaliteli `Model Name` sütunu modelden çıkarılmamış ve one-hot kodlamaya dahil edilmiştir.
- Cross-Validation, artık (residual) analizi ve ağaç/düzenlileştirilmiş (Ridge/Lasso) model karşılaştırmaları eksiktir.

## Veri ve Çalıştırma

```bash
pip install -r ../../requirements.txt
kaggle datasets download -d rafsunahmad/plane-price-prediction
jupyter lab notebook.ipynb
```

Notebook Kaggle ortamında doğrudan çalışır. Yerelde çalıştırmak için `Plane Price.csv` dosyasını `data/` klasörüne koyup dosya yolunu uyarlayabilirsiniz.
