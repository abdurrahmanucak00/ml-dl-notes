# 09 · Sınıf Dengesizliği, Eşik ve Pipeline

## A. Dengesizlik

> Resampling **sadece CV içinde train fold'a** uygulanır. Validation/test gerçek dağılımda kalır.

Müdahale sırası (en güvenliden en riskliye):

1. **Hiçbir şey yapma + doğru metrik:** orta dengesizlikte (%15–85) ağaç modeliyle PR-AUC / Log-Loss.
2. **Maliyet duyarlı öğrenme:** `class_weight="balanced"`, XGBoost `scale_pos_weight = neg/pos`, LightGBM `is_unbalance` / `scale_pos_weight`.
3. **Eşik optimizasyonu:** OOF olasılıklarda precision-recall eğrisini tara; iş hedefine göre F1/F2 veya maliyet minimum olan eşiği seç. Varsayılan 0.5 dengesiz veride genelde yanlıştır.
4. **SMOTE / undersampling:** son çare. Yüksek boyutta SMOTE gürültü üretebilir. Undersampling sonrası olasılıkları kalibre et.

Metrikler: PR-AUC, F-beta, recall@precision, confusion matrix. Accuracy tek başına yanıltır.

## B. Pipeline

Tüm adımları `Pipeline` + `ColumnTransformer` içine koy:

```python
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import OneHotEncoder, StandardScaler
from sklearn.linear_model import LogisticRegression

num = Pipeline([("imp", SimpleImputer(strategy="median")), ("sc", StandardScaler())])
cat = Pipeline([("imp", SimpleImputer(strategy="constant", fill_value="None")),
                ("oh", OneHotEncoder(handle_unknown="ignore"))])
pre = ColumnTransformer([("num", num, num_cols), ("cat", cat, cat_cols)])
pipe = Pipeline([("pre", pre), ("model", LogisticRegression(max_iter=1000))])
```

Faydası: CV içinde `fit(train)`/`transform(val)` ayrımı otomatik, tek `.joblib` ile kaydedilir.

## C. Bitirmeden önce

- [ ] Tüm veride `.fit()`, `.mean()`, `get_dummies()` çağrısı yok
- [ ] Bilinmeyen kategori ve beklenmedik `NaN` pipeline'ı çökertmiyor
- [ ] Oran özelliklerinde sıfıra bölme korunmuş
- [ ] Olasılıklar karar için kullanılacaksa kalibrasyon (Brier, calibration curve) bakıldı
- [ ] Hata analizi: model en çok hangi örneklerde yanılıyor?

## Kendine sor

- Dengesizlik gerçekten sorun muydu? Önce hiçbir şey yapmadan skora baktım mı?
- Eşiği hangi iş hedefine göre seçtim?
