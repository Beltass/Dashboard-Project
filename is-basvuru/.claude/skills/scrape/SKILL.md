---
name: scrape
description: LinkedIn ve kariyer.net üzerinde `profil.md`'deki hedef unvanlara uygun iş ilanlarını tarar, tekrarları başvuru takip günlüğüyle eler ve kısa bir aday listesi çıkarır. Kullanıcı "iş ara", "yeni ilan var mı", "kariyer.net'te tara" gibi bir istekte bulunduğunda kullanın. Önce `setup` becerisinin çalıştırılmış olması gerekir.
---

# Scrape — İlan Tarama

Bu beceri iş ilanlarını **bulur ve listeler**; başvuru materyali hazırlamaz
(bkz. `apply`) ve hiçbir siteye otomatik başvuru göndermez.

## Kapsam ve Sınırlar (önce oku)

- Yalnızca **LinkedIn** ve **kariyer.net** hedeflenir (kullanıcı başka bir
  site isterse önce onunla teyitleş).
- Yalnızca **herkese/oturum sahibine görünen arama sonucu sayfalarını**
  gez; giriş duvarını aşmaya, CAPTCHA'yı atlatmaya veya oturum
  kimlik bilgilerini otomatikleştirmeye ÇALIŞMA. Kullanıcı zaten
  giriş yapmış bir tarayıcı oturumunda çalışıyorsa o oturumu kullan.
- İsteklerini makul sıklıkta yap (art arda hızlı istek yok); bir site
  erişimi engelliyor/CAPTCHA gösteriyorsa ısrar etme, kullanıcıya bildir.
- Bu beceri **hiçbir formu göndermez, hiçbir "Kolay Başvuru" düğmesine
  tıklamaz**. Bulunan ilanlar yalnızca inceleme için sunulur.

## Adımlar

1. **Hedef kriterleri yükle.**
   `.claude/skills/apply/references/profil.md` içindeki "Hedef Rol Anahtar
   Kelimeleri" bölümünü oku: unvanlar (Customer Experience Manager/Director,
   Contact Center Operations Manager, Account Manager, Vendor/Outsource
   Management, Müşteri Deneyimi Yöneticisi, Çağrı Merkezi Operasyon
   Yöneticisi) ve konum (İstanbul, Türkiye; uzaktan/hibrit dahil).

2. **Arama URL'lerini oluştur / kullanıcıdan al.**
   Kullanıcı belirli bir arama URL'si vermediyse, kariyer.net ve LinkedIn
   iş arama sonuç sayfaları için hedef unvan + İstanbul filtresiyle bir
   arama URL'si öner ve kullanıcıdan teyit/URL iste (arama sonucu
   sayfalarının yapısı sık değiştiği için elle doğrulanmış bir URL en
   güvenilir yoldur).

3. **İlanları çek ve ayrıştır.**
   Her ilan için şu alanları çıkar: şirket, unvan, konum, çalışma şekli
   (yerinde/hibrit/uzaktan), ilan URL'si, yayın tarihi (varsa), kısa
   açıklama özeti.

4. **Tekrarları ele.**
   `basvurular/takip.csv` dosyasındaki `ilan_url` sütunuyla karşılaştır;
   zaten kayıtlı olan ilanları listeleme (veya "zaten değerlendirildi"
   notuyla ayrı göster).

5. **Kısa liste sun.**
   Kullanıcıya bulunan ilanları tablo halinde özetle: şirket, unvan,
   konum, kaynak (LinkedIn/kariyer.net), URL. Ön eleme/puanlama yapma —
   bu iş `apply` becerisinin `reviewer-kriterleri.md`'ye göre yaptığı iş.

6. **Sıradaki adımı sor.**
   Kullanıcıya hangi ilan(lar) için `apply` becerisiyle başvuru materyali
   hazırlanacağını sor.

## Notlar

- Bu beceri site kullanım şartlarına (ToS) saygılı, düşük hacimli, insan
  gözetiminde bir tarama aracıdır — toplu/otomatik veri kazıma veya bot
  başvurusu amaçlı değildir.
