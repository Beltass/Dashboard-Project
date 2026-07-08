---
name: setup
description: İş başvuru asistanının (is-basvuru) çalışma ortamını hazırlar — profil verisini, CV şablonunu, gerekli araçları ve başvuru takip günlüğünü kontrol eder/oluşturur. `scrape` veya `apply` becerilerini ilk kez kullanmadan önce, ya da ortam bozulduğunda çalıştırın.
---

# Setup — İş Başvuru Ortamı Kurulumu

`is-basvuru` üç beceriden oluşur: `setup` (bu beceri), `scrape` (ilan tarama)
ve `apply` (başvuru materyali hazırlama). Bu beceri diğer ikisinin
güvenilir çalışması için gereken ön koşulları doğrular.

## Yapılacaklar

1. **Profil dosyasını doğrula.**
   `.claude/skills/apply/references/profil.md` mevcut mu ve boş değil mi
   kontrol et. Yoksa kullanıcıdan CV/profil bilgisi iste — bu dosya
   olmadan `apply` becerisi çalışamaz.

2. **CV şablonunu doğrula.**
   `.claude/skills/apply/references/cv-sablonu.tex` mevcut mu kontrol et.
   Dosyanın başındaki yorum bloğunda hangi komutların (`\profilOzet`,
   `\hedefUnvan`) ilana göre kişiselleştirilebileceği belirtilmiştir —
   bunun dışındaki olgusal içerik (deneyim, tarihler, rakamlar) `profil.md`
   ile birebir eşleşmelidir; tutarsızlık varsa kullanıcıyı uyar.

3. **LaTeX derleme aracını kontrol et.**
   `pdflatex` (veya `tectonic`) PATH'te var mı diye bak:
   ```bash
   command -v pdflatex || command -v tectonic
   ```
   Yoksa kullanıcıya haber ver ve kurulumu teklif et (ör. Debian/Ubuntu'da
   `apt-get install -y --no-install-recommends texlive-latex-base
   texlive-latex-recommended texlive-latex-extra`). Şablon şu paketleri
   gerektirir: `xcolor`, `amssymb`, `paracol`, `enumitem`, `changepage`,
   `eso-pic`, `hyperref` (çoğu `texlive-latex-extra` ile gelir).
   **Önemli:** `babel[turkish]` KASITLI olarak kullanılmaz — bu paket
   `hyperref` + `enumitem` ile birlikte yüklendiğinde ikinci renkli
   madde işaretli `itemize` ortamında derleme hatası veriyor
   (doğrulanmış paket çakışması, `cv-sablonu.tex` başındaki yoruma bak).
   Şablonu değiştirirken bu paketi geri eklemeyin.

4. **Çalışma dizinlerini oluştur.**
   Depo kökünde (bu dosyanın 3 üst dizini) şu klasörleri oluştur, yoksa:
   - `basvurular/` — her başvuru için üretilen CV/ön yazı PDF'lerinin
     kaydedildiği klasör (dosya adı: `basvurular/<sirket>-<tarih>.pdf`).
   - `basvurular/takip.csv` — başvuru takip günlüğü. Sütunlar:
     `tarih,sirket,unvan,ilan_url,kaynak,uygunluk_puani,durum,not`.
     `durum` alanı: `taslak-hazir`, `basvuruldu`, `elendi`, `atlandi`.

   Dosya yoksa başlık satırıyla oluştur:
   ```csv
   tarih,sirket,unvan,ilan_url,kaynak,uygunluk_puani,durum,not
   ```

5. **Özet raporla.** Hangi öğelerin hazır olduğunu (profil, şablon,
   pdflatex, takip günlüğü) ve eksik/dikkat gereken noktaları kullanıcıya
   kısaca bildir.

## Kapsam Dışı

Bu beceri hiçbir web sitesine bağlanmaz, hiçbir başvuru göndermez —
sadece yerel ortamı hazırlar. Tarama için `scrape`, başvuru materyali
için `apply` becerisini kullanın.
