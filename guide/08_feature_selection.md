# 08 · Özellik Seçimi ve Boyut İndirgeme

Amaç: gürültüyü azaltmak, overfitting'i engellemek, eğitimi hızlandırmak. Her eleme CV ile doğrulanır; skor düşüyorsa eleme yapılmaz.

## Eleme hunisi

1. **Filtre (hızlı)**
   - `VarianceThreshold`.
   - Birbirine çok benzeyen sütunlardan (`|r| > 0.98`) hedefle ilişkisi zayıf olanı at.
2. **Gömülü yöntemler**
   - L1 (Lasso) katsayıları sıfırlananları ele.
   - Hızlı LightGBM ile gain'i 0 olanları ele.
3. **Permutation importance (validation üzerinde)**
   - Sütunu karıştırınca skor düşmüyorsa veya artıyorsa sütun gürültüdür.
   - Ağaçların yerleşik önemi yüksek kardinaliteli sütunlara yanlıdır, o yüzden tek başına yetmez.
4. **SHAP**
   - Hem önem hem yön. İş mantığına ters veya sızıntıya benzeyen özellikleri yakalar.
5. **Gölge özellik (Boruta / null importance)**
   - Karıştırılmış kopyalardan daha önemli olmayan özelliği ele.

## Boyut indirgeme

- PCA: scaling sonrası, `fit` sadece train. Yorumlanabilirliği düşürür.
- Metin: `TruncatedSVD` (LSA).
- Görselleştirme için t-SNE/UMAP; modelde girdi olarak kullanmadan önce dikkat.

## Kendine sor

- Elenen sütunlar listesi ve CV skoruna etkisi?
- Elediğim sütun alan bilgisi açısından önemliydi mi?
