# 09 · Sınıf Dengesizliği, Eşik ve Pipeline

**Neden bu adım var?**
Gerçek dünya sınıflandırma problemlerinde (dolandırıcılık, hastalık tespiti, abonelik tahmini) ilgilendiğimiz pozitif sınıf genelde çok azdır (%1–%15). Standart modeller çoğunluk sınıfına odaklanıp azınlık sınıfını gözden kaçırır. İkinci olarak, tüm ön işleme adımlarını tek bir `Pipeline` içinde toplamak, hem CV sırasında veri sızıntısını imkansız hale getirir hem de canlı veride aynı adımların eksiksiz çalışmasını garanti eder.

## Temel (Önce Bunu Uygula)

### A. Sınıf Dengesizliğine Müdahale Sırası (En Güvenliden En Riskliye)
1. **Doğru metrik + Baseline:** Accuracy'ye değil `F1`, `Recall`, `PR-AUC` metriklerine bak; majority baseline'ı hesapla.
2. **Maliyet duyarlı ağırlıklandırma (ilk tercih):** Veriyi bozmadan azınlık sınıfı hatalarını daha ağır cezalandır:
   - Scikit-learn modelleri: `class_weight="balanced"`
   - XGBoost: `scale_pos_weight = (y_train == 0).sum() / (y_train == 1).sum()` *(DİKKAT: yalnızca `y_train`'den hesaplanır!)*
   - LightGBM: `is_unbalance=True` veya `scale_pos_weight`

### B. Tüm Adımları Tek `Pipeline` İçinde Birleştir
`ColumnTransformer` sayısal ve kategorik sütunları ayrı işler; `Pipeline` ise ön işlemeyi modele bağlar:

```python
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import OneHotEncoder, StandardScaler
from sklearn.linear_model import LogisticRegression

num_pipe = Pipeline([
    ("imputer", SimpleImputer(strategy="median")),
    ("scaler",  StandardScaler())
])
cat_pipe = Pipeline([
    ("imputer", SimpleImputer(strategy="most_frequent")),
    ("encoder", OneHotEncoder(handle_unknown="ignore"))
])

preprocessor = ColumnTransformer([
    ("num", num_pipe, num_cols),
    ("cat", cat_pipe, cat_cols)
])

pipe = Pipeline([
    ("preprocessor", preprocessor),
    ("model", LogisticRegression(max_iter=1000, class_weight="balanced"))
])
```

**Pipeline'ın 2 büyük faydası:**
1. `cross_val_score` ve `GridSearchCV` içinde her fold için `fit(train)` / `transform(val)` ayrımını otomatik yapar — leakage riskini sıfırlar.
2. Tek bir nesne olarak kaydedilir (bkz. [11 · Hata Analizi ve Model Kaydetme](11_error_analysis_and_export.md)).

## İleri Seviye (İhtiyaç Olduğunda)

- **Karar eşiği optimizasyonu (Threshold Tuning):** Modeller varsayılan olarak `0.50` olasılık eşiğini kullanır; dengesiz veride bu nadiren optimaldir. OOF olasılıkları (`predict_proba`) üzerinde Precision–Recall eğrisini tarayarak maksimum F1 veya iş maliyetini minimize eden eşiği (ör. `0.22`) seç.
- **SMOTE / Undersampling (son çare):** Yeniden örnekleme **yalnızca CV döngüsü içindeki train fold'a** uygulanır (`imblearn.pipeline.Pipeline` ile). Doğrulama ve test setleri her zaman gerçek dağılımında kalır. Undersampling sonrası olasılık kalibrasyonu (`CalibratedClassifierCV`) kontrol edilmelidir.

## Kendine Sor

- `scale_pos_weight` veya `SMOTE` gibi işlemleri yalnızca `train` verisi üzerinde mi uyguladım?
- Eğitimde çıkardığım `id` gibi sütunları tahmin anında gelen test verisinden de çıkarıyor muyum (veya `ColumnTransformer` bunları güvenle düşürüyor mu)?
