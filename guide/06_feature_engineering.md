# 06 · Özellik Mühendisliği

Ham veride gizli alan bilgisini modelin tüketebileceği sinyale çevir. Her yeni özellik CV ile test edilir.

## 1. Alan bilgisi oranları ve toplamlar
- Oran: `borç / gelir`, `fiyat / m²`, `tıklama / gösterim`.
- Toplam: birden çok alan sütununu birleştir (toplam metrekare, toplam banyo).
- Yaş / süre: `şimdiki yıl - yapım yılı`, `satış yılı - yenileme yılı`.
- Payda sıfır olabilir: `+ 1e-5` veya `np.isinf` temizliği.

## 2. Zaman özellikleri
- Yıl, ay, gün, haftanın günü, hafta sonu, tatil.
- Döngüsel özellikler (doğrusal/NN için): `sin(2πx/T)`, `cos(2πx/T)`; saat T=24, ay T=12, haftanın günü T=7.
- Lag / rolling (zaman serisi): mevcut satırı hariç tut (`shift(1).rolling(7).mean()`), veri `[grup, tarih]` sıralı olmalı.

## 3. Grup bazlı istatistikler
- Gruba göre `mean/median/std`, değerin grup ortalamasından farkı/oranı.
- **Sadece train'den hesapla**, val/test'e `merge` et. Aksi halde sızıntı.

## 4. Etkileşim ve polinom
- Kategorik kombinasyon (`şehir + "_" + meslek`).
- Doğrusal modeller için `PolynomialFeatures(degree=2, interaction_only=True)`.

## 5. Metin özellikleri (NLP)
- Yapısal: karakter/kelime sayısı, büyük harf oranı, noktalama sayısı, emoji sayısı.
- Vektörleme: TF-IDF (word + char n-gram), gerekirse `TruncatedSVD`; embedding.
- Türkçe için: eklemeli yapı nedeniyle char n-gram genelde işe yarar, lemmatization/stemming denemesi CV ile karşılaştırılır.

## 6. Sayaç ve varlık göstergeleri
- "Havuz var mı", "ikinci kat var mı" gibi `0/1` göstergeleri; ham alan sütunu sıfır yığılmalıysa işe yarar.

## Kendine sor

- Bu özellik tahmin anında hesaplanabilir mi?
- Skora etkisi CV std'sinden büyük mü? Değilse neden tutuyorum?
