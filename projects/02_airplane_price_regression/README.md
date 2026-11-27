# 02 · Uçak Fiyat Tahmini

| Özellik | Değer |
|---|---|
| Seviye | Başlangıç |
| Problem Tipi | Tabular Regresyon |
| Hedef Değişken | `Price` (`517` satır, hedefi dolu `507` satır) |
| Veri Kaynağı | [Kaggle — rafsunahmad/plane-price-prediction](https://www.kaggle.com/datasets/rafsunahmad/plane-price-prediction) |

**Bu projede ne öğrenirsin?**
Bir önceki projenin üzerine **sızıntısız ön işleme** eklenir: eksik veri dağılımlarını (`histplot`) inceleme, `Rcmnd cruise Knots` sütunundaki `.median()` parantez hatası gibi sessiz ön işleme hatalarından kaçınma, `train_test_split` sonrası yalnızca `x_train`'den öğrenilen imputation/encoding (`reindex`) ve `StandardScaler` kullanımı.

## Rehber Eşleme Tablosu (`guide/`)

| # | Adım | Kapsam | Not |
|---|---|---|---|
| 01 | [Problem, metrik ve baseline](../../guide/01_problem_metric_baseline.md) | Kısmi | `R²`, `RMSE`, `MAE` hesaplandı; `DummyRegressor` baseline eksik |
| 02 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | Kısmi | Hedefi eksik satırlar atıldı; sayısal maskeli metin sütunları temizlenmedi (aşağıya bakınız) |
| 03 | [EDA ve veri sızıntısı avı](../../guide/03_eda_and_leakage.md) | Uygulandı | Eksik sütunlar için `histplot` incelendi; split öncesi imputation/encoding sızıntısı giderildi |
| 04 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | Kısmi | %20 holdout `train_test_split` uygulandı; Cross-Validation eksik |
| 05 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | Uygulandı | `x_train` medyan/modu ile sızıntısız doldurma; aykırı değer müdahalesi yok |
| 06 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | Atlandı | |
| 07 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | Uygulandı | `x_train`/`x_test` ayrı `get_dummies` + `reindex` ve `StandardScaler` |
| 08 | [Özellik seçimi](../../guide/08_feature_selection.md) | Atlandı | |
| 09 | [Dengesiz veri ve pipeline](../../guide/09_imbalance_and_pipeline.md) | Atlandı | `sklearn.pipeline.Pipeline` kullanılmadı |
| 10 | [Model seçimi ve tuning](../../guide/10_modeling_and_tuning.md) | Kısmi | Tek model (`LinearRegression`) kuruldu |
| 11 | [Hata analizi ve kaydetme](../../guide/11_error_analysis_and_export.md) | Atlandı | Artık (residual) analizi ve model kaydetme yok |

## Sonuçlar (Holdout Doğrulama — %20 `train_test_split`)

> **Not:** Tüm metrikler `train_test_split(..., test_size=0.2, random_state=42)` ile ayrılan **%20 holdout doğrulama seti** üzerinde hesaplanmıştır.

| Model | Holdout R² | RMSE | MAE |
|---|---|---|---|
| LinearRegression (düzeltilmiş) | **0.7257** | **505,871.19** | **358,087.70** |

> **Skor Değişimi Notu (`0.8036` → `0.7257`):** Orijinal notebook'taki `0.8036` R² skorundan `0.7257`'ye değişim tek başına veri sızıntısının giderilmesine bağlanamaz; split sonrası imputation/`get_dummies` (`reindex`), `Rcmnd cruise Knots` `.median()` bug düzeltmesi ve `StandardScaler` üzerinde eğitim birlikte uygulanmıştır (izole ablasyon yapılmamıştır).

## Bilinen Sorunlar ve Eksikler
- **Sayıya Çevrilmeyen `object` Sütunlar:** Sayısal özellik taşımasına rağmen metin (`object`) olarak okunan `HP or lbs thr ea engine`, `Max speed Knots`, `All eng rate of climb`, `Landing over 50ft`, `Empty weight lbs`, `Length ft/in`, `Wing span ft/in`, `Range N.M.` sütunları temizlenip sayısala çevrilmeden `pd.get_dummies` ile kategorik olarak kodlanmıştır.
- **`Model Name` Kimlik Sütunu:** Yüksek kardinaliteli `Model Name` sütunu modelden çıkarılmamış ve one-hot kodlamaya dahil edilmiştir.
- Cross-Validation, artık (residual) analizi ve düzenlileştirilmiş/ağaç model karşılaştırmaları eksiktir.

## Veri ve Çalıştırma

```bash
pip install -r ../../requirements.txt
kaggle datasets download -d rafsunahmad/plane-price-prediction
jupyter lab notebook.ipynb
```

Notebook Kaggle ortamında doğrudan çalışır. Yerelde çalıştırmak için `Plane Price.csv` dosyasını `data/` klasörüne koyup dosya yolunu uyarlayabilirsiniz.

---
[← Önceki Proje: 01 · Audi A1](../01_audi_a1_price_regression/README.md) · [Sonraki Proje: 03 · Titanic →](../03_titanic_survival/README.md)
