# 04 · Bölme Stratejileri ve Doğrulama

Gerçek hayat senaryosunu taklit eden, sızıntısız bir doğrulama kur. Bu adım imputation, scaling ve encoding'den **önce** gelir.

| Veri yapısı | Strateji | Neden |
|-------------|----------|-------|
| Bağımsız tablo | `KFold` (5 veya 10) | Satırlar bağımsız |
| Sınıflandırma | `StratifiedKFold` | Her fold'da sınıf oranı korunur |
| Dengesiz regresyon | Hedefi quantile'lara bölüp `StratifiedKFold` | Uç değerler tek fold'a yığılmaz |
| Gruplu veri (hasta, müşteri, kullanıcı) | `GroupKFold` / `StratifiedGroupKFold` | Aynı grup iki tarafa düşmez |
| Zaman serisi | `TimeSeriesSplit` (+ embargo) | Gelecek geçmişi tahmin edemez |

## Pratik akış

1. Test/hold-out ayır (gerekiyorsa) ve bir daha dokunma.
2. Kalan veride CV kur.
3. Tüm dönüşümler fold içinde `fit(train)`, `transform(val)`.
4. OOF (out-of-fold) tahminleri sakla: eşik optimizasyonu, ensemble ve hata analizi bunlarla yapılır.

```python
from sklearn.model_selection import StratifiedKFold, cross_val_score
cv = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)
scores = cross_val_score(pipe, X, y, cv=cv, scoring="roc_auc")
print(scores.mean(), scores.std())
```

## Dikkat

- CV skorunun std'si farklar için eşik belirler: fark std'den küçükse iyileşme gerçek olmayabilir.
- Aynı CV ile çok fazla hiperparametre denersen validation'a overfit olursun; nested CV veya ayrı hold-out düşün.
- Tekrarlanabilirlik: `random_state` sabit.

## Kendine sor

- Neden bu CV stratejisi? Satırlar gerçekten bağımsız mı?
- Split, hangi dönüşümden önce yapıldı?
