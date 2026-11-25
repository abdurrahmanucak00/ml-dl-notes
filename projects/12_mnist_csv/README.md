# MNIST in CSV

| | |
|---|---|
| Kaynak | Kaggle dataset (Kaggle'da adıyla ara) |
| Tip | Görüntü (DL ağırlıklı) |
| Görev / metrik (öneri) | Rakam sınıflandırma (10 sınıf). Metrik: accuracy. DL bölümüyle bağlantılı |
| Durum | ⬜ başlanmadı |

**Bu projede ne öğretiyor:** Piksel verisini hazırlama, MLP/CNN baseline'ı ve overfit takibi.

## Checklist'in hangi adımları kullanıldı

| # | Adım | Durum | Not (ne yaptım / neden atladım) |
|---|------|-------|---------------------------------|
| 1 | [Problem, metrik, baseline](../../guide/01_problem_metric_baseline.md) | ⬜ uygulanacak | |
| 2 | [İlk temas ve temizlik](../../guide/02_first_look_and_cleaning.md) | ⬜ uygulanacak | |
| 3 | [EDA ve sızıntı avı](../../guide/03_eda_and_leakage.md) | ⬜ uygulanacak | |
| 4 | [Bölme ve doğrulama](../../guide/04_splitting_and_validation.md) | ⬜ uygulanacak | |
| 5 | [Eksik ve aykırı değer](../../guide/05_missing_values_and_outliers.md) | ➖ muhtemelen atlanacak | |
| 6 | [Özellik mühendisliği](../../guide/06_feature_engineering.md) | ➖ muhtemelen atlanacak | |
| 7 | [Encoding, scaling, vektörleme](../../guide/07_encoding_and_scaling.md) | ⬜ uygulanacak | |
| 8 | [Özellik seçimi](../../guide/08_feature_selection.md) | ➖ muhtemelen atlanacak | |
| 9 | [Dengesizlik ve pipeline](../../guide/09_imbalance_and_pipeline.md) | ⬜ uygulanacak | |

Durumu bitirince `✅ uygulandı` veya `➖ atlandı (gerekçe)` olarak güncelle.

## Sonuçlar

| Deney | CV skor |
|-------|---------|
| Dummy | |
| Ham veri + basit model | |
| Final pipeline | |

## Öne çıkan öğrenme

- 

## Veri

Veri repoya eklenmez (`.gitignore`). Kaggle'dan indirip `data/` klasörüne koy.

## Çalıştırma

```bash
pip install -r ../../requirements.txt
jupyter lab notebook.ipynb
```
