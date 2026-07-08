---
name: apply
description: Belirli bir iş ilanı için uygunluğu değerlendirir, uygunsa `cv-sablonu.tex` ve `profil.md`'yi kullanarak ilana özel CV (PDF) ve kısa bir ön yazı/mesaj taslağı hazırlar, başvuru takip günlüğüne kaydeder. Kullanıcı "şu ilana başvur/başvuru hazırla" dediğinde veya `scrape` becerisinin bulduğu bir ilan seçildiğinde kullanın. Gönderim otomatik yapılmaz — kullanıcı taslağı inceleyip kendisi gönderir.
---

# Apply — Başvuru Materyali Hazırlama

## Referans Dosyalar

- `references/profil.md` — tüm olgusal içeriğin (deneyim, tarihler,
  rakamlar, unvanlar) tek doğruluk kaynağı. Değiştirilmez, sadece okunur.
- `references/cv-sablonu.tex` — LaTeX CV şablonu. Dosyanın başında
  işaretlenen `\profilOzet` ve `\hedefUnvan` komutları dışındaki içerik
  `profil.md` ile birebir eşleşmelidir.
- `references/reviewer-kriterleri.md` — başvurmadan önce uygulanacak
  100 puanlık ATS/recruiter puanlama rubriği ve eşik değerleri.

## Adımlar

1. **İlan metnini al.** Kullanıcıdan ilan URL'si veya yapıştırılmış ilan
   metni iste (yoksa `scrape` çıktısından seç).

2. **Uygunluk puanla.** `reviewer-kriterleri.md`'deki rubriğe göre
   ilanı `profil.md` ile karşılaştırıp 0-100 arası puanla; her kriter için
   kısa gerekçe çıkar.
   - **< 50 puan:** Başvuru hazırlama; kullanıcıya neden uygun olmadığını
     kısaca açıkla ve `basvurular/takip.csv`'ye `durum=atlandi`, nedeniyle
     birlikte kaydet. Burada dur.
   - **50–69 puan:** Uyum notunu kullanıcıya göster, devam edip
     etmeyeceğini sor.
   - **≥ 70 puan:** Devam et.

3. **CV'yi kişiselleştir.**
   `cv-sablonu.tex`'i `basvurular/<sirket-kisa-ad>-cv.tex` olarak kopyala.
   Yalnızca şunları güncelle:
   - `\profilOzet`: 2-4 cümleye indir, ilanın öne çıkan anahtar
     kelimelerini vurgula. Rakamları/olguları değiştirme.
   - `\hedefUnvan`: ilanın unvanına yakın ama `profil.md`'deki gerçek
     kariyer geçmişiyle çelişmeyecek şekilde uyarla.
   - Sidebar'daki `YETKİNLİKLER` listesini ilana göre yeniden sırala
     (madde ekleme/çıkarma yapma — `profil.md` ile aynı küme kalmalı).
   Deneyim, eğitim, sertifika, ödül bölümlerine dokunma.

4. **PDF'e derle.**
   ```bash
   pdflatex -interaction=nonstopmode -halt-on-error basvurular/<sirket>-cv.tex
   ```
   Derleme hatası alırsan `cv-sablonu.tex` başındaki notu kontrol et
   (özellikle `babel[turkish]` paketinin eklenmediğinden emin ol — bu
   paket `hyperref`+`enumitem` ile ikinci renkli madde işaretinde
   derleme hatasına yol açıyor, doğrulanmış bir çakışma).

5. **ATS uyumluluğunu kontrol et.**
   İlan bir ATS portalı (Workday, Lever, Greenhouse vb.) üzerinden
   başvuru istiyorsa, iki sütunlu PDF'e ek olarak düz metin/tek sütun bir
   CV özeti üretmeyi teklif et (bkz. `reviewer-kriterleri.md` >
   "ATS Format Uyumluluğu Notları"). LinkedIn Kolay Başvuru / e-posta
   başvurusu için iki sütunlu PDF yeterlidir.

6. **Kısa ön yazı/mesaj taslağı yaz.**
   3-5 cümlelik, ilana özel, `profil.md`'deki somut başarılardan 1-2
   tanesini referans veren bir ön yazı/LinkedIn mesajı taslağı hazırla.
   Klişe/şişirilmiş dil kullanma; olgulara sadık kal.

7. **Kullanıcıya sun, GÖNDERME.**
   Üretilen PDF ve ön yazı taslağını kullanıcıya sun. Bu beceri hiçbir
   platforma otomatik başvuru göndermez, hiçbir form doldurmaz/submit
   etmez — kullanıcı içeriği inceleyip kendisi başvurur.

8. **Takip günlüğüne kaydet.**
   `basvurular/takip.csv`'ye bir satır ekle: tarih, şirket, unvan, ilan
   URL'si, kaynak, uygunluk puanı, `durum=taslak-hazir`, kısa not.
   Kullanıcı başvuruyu fiilen gönderdiğini bildirirse `durum=basvuruldu`
   olarak güncelle.

## Yapılmayacaklar

- `profil.md`'deki rakamları, unvanları, tarihleri abartma veya uydurma.
- Puanı 50'nin altındaki ilanlar için taslak üretme.
- Herhangi bir siteye otomatik form gönderimi/"Kolay Başvuru" tıklaması
  yapma — bu, platform kullanım şartlarını ihlal eder ve bu becerinin
  kapsamı dışındadır.
- Aynı ilan için `basvurular/takip.csv`'de zaten kayıt varsa tekrar
  taslak üretmeden önce kullanıcıya sor.
