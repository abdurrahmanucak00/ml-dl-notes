# 10 · Model Seçimi ve Hiperparametre Optimizasyonu (Tuning)

**Neden bu adım var?**
Veriyi temizleyip sızıntısız pipeline'ı kurduktan sonra "en karmaşık algoritmayı seçeyim" demek yerine, problemi iyi tanıyan sade bir modelle başlayıp sistematik olarak ilerlemek gerekir. Bir modelin ezberleyip ezberlemediğini (overfitting) anlamadan hiperparametre aramak hem zaman kaybıdır hem de skoru iyileştirmez.

## Temel (Önce Bunu Uygula)

### 1. Probleme göre hangi modelle başlanır?

| Veri / Problem Tipi | Hızlı Baseline | Güçlü Ana Adaylar | Not |
|---------------------|----------------|-------------------|-----|
| Tabular Regresyon | `Ridge` / `LinearRegression` | `RandomForestRegressor`, `XGBRegressor`, `LightGBM` | Doğrusal modeller scaling ister |
| Tabular Sınıflandırma | `LogisticRegression` | `RandomForestClassifier`, `XGBClassifier`, `LightGBM` | Ağaçlar doğrusal olmayan ilişkileri iyi yakalar |
| Metin (NLP — TF-IDF) | `MultinomialNB`, `LogisticRegression` | `LinearSVC`, (ileri aşamada Transformer) | Yüksek boyutlu seyrek matrislerde lineer modeller çok güçlüdür |

### 2. Overfitting vs. Underfitting teşhisi (Train vs. CV skoru)

Modeli eğittikten sonra **eğitim (train) skoru ile doğrulama (CV) skorunu** yan yana koy:

| Durum | Belirti | Ne Yapmalı? |
|-------|---------|-------------|
| **Underfitting** (Yetersiz öğrenme) | Hem Train hem CV skoru düşük | Daha güçlü model dene, ağaç derinliğini (`max_depth`) artır, yeni özellikler üret |
| **Overfitting** (Aşırı öğrenme / ezberleme) | Train çok yüksek (`%99`), CV belirgin düşük (`%78`) | Regularizasyonu artır (`max_depth` küçült, `min_samples_leaf` büyüt, `C` küçült / `alpha` büyüt), gürültülü sütunları çıkar, daha fazla veri ekle |
| **Dengeli** | Train ve CV birbirine yakın ve yüksek | Hiperparametre ince ayarı ve hata analizine geç |

### 3. `Pipeline` içinde `GridSearchCV` nasıl yazılır?
Pipeline içindeki bir adımın parametresini ararken **`adım_adı__parametre_adı`** (çift alt çizgi) kullanılır:

```python
from sklearn.model_selection import GridSearchCV

param_grid = {
    "model__max_depth": [3, 5, 7],
    "model__learning_rate": [0.05, 0.1],
    "model__n_estimators": [100, 200],
}

grid = GridSearchCV(
    estimator=pipe,
    param_grid=param_grid,
    cv=5,
    scoring="f1",     # Problemine uygun metriği açıkça ver!
    n_jobs=-1
)
grid.fit(X_train, y_train)

print("En iyi parametreler:", grid.best_params_)
print("En iyi CV skoru    :", f"{grid.best_score_:.4f}")
best_model = grid.best_estimator_
```

## İleri Seviye (İhtiyaç Olduğunda)

- **`RandomizedSearchCV` / `Optuna`:** Parametre uzayı büyüdüğünde tüm kombinasyonları deneyen `GridSearchCV` çok yavaşlar; `RandomizedSearchCV` veya Bayesian arama (`Optuna`) çok daha az denemede iyi sonuç bulur.
- **Early Stopping (XGBoost / LightGBM):** Sabit `n_estimators` aramak yerine, doğrulama skoru `N` tur boyunca iyileşmeyince eğitimi otomatik durdur.
- **Ensemble (Model Birleştirme):** Farklı ailelerden iki iyi modelin (ör. `Ridge` + `LightGBM`) OOF tahminlerinin ağırlıklı ortalamasını almak çoğu zaman tekil modellerin ikisini de geçer.

## Kendine Sor

- Train ve CV skorlarını karşılaştırıp overfitting veya underfitting teşhisi koydum mu?
- `GridSearchCV` içinde varsayılan `accuracy` yerine gerçekten problemime uygun `scoring` metriğini mi verdim?
