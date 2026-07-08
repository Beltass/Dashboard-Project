# Reviewer / ATS Kriterleri

Bu dosya, `apply` becerisinin bir ilana başvurmadan ÖNCE "bu ilana gerçekten
uygun muyum, zaman ayırmaya değer mi?" sorusunu yanıtlamak için kullandığı
genel ATS (Applicant Tracking System) ve insan kaynakları değerlendirme
kriterleridir. Belirli bir şirkete/role özel değildir — herhangi bir ilan
için aynı puanlama mantığı uygulanır.

Amaç: düşük uyumlu ilanlara otomatik/gelişigüzel başvuru yapmayı (spam)
önlemek ve zamanı yüksek olasılıklı fırsatlara ayırmak.

## Puanlama Rubriği (100 puan üzerinden)

| Kriter | Ağırlık | Açıklama |
|---|---|---|
| Unvan / kıdem uyumu | 20 | İlanın unvanı `profil.md`'deki geçmiş unvanlar ve kariyer hedefiyle örtüşüyor mu? (Manager/Director seviyesi vs. Uzman/Executive seviyesi karışıklığına dikkat.) |
| Anahtar kelime eşleşmesi | 20 | İlan metnindeki teknik/yetkinlik terimleri (`profil.md > Hedef Rol Anahtar Kelimeleri` ile karşılaştır) ne kadar örtüşüyor? ATS sistemleri çoğunlukla bu eşleşmeye göre ön eleme yapar. |
| Deneyim yılı / kapsam uyumu | 15 | İlan "X+ yıl deneyim", "Y kişilik ekip yönetimi", "Z ölçekli operasyon" gibi somut eşikler istiyorsa, profildeki 17 yıl / 150+ kişi / 500.000+ kullanıcı rakamları bu eşikleri karşılıyor mu? |
| Konum / çalışma şekli | 15 | İstanbul ofis, hibrit veya tam uzaktan mı? Profildeki konum (İstanbul, Türkiye) ile uyumsuzsa (ör. yurt dışı ofis şartı, günlük başka şehir) puan düşürülür. |
| Sektör / alan uyumu | 10 | Telekom, fintech, çağrı merkezi teknolojisi, BPO/outsource, CX/CRM SaaS gibi alanlar yüksek uyum; tamamen alakasız bir sektör (ör. üretim hattı operasyonu, saha satışı) düşük uyum. |
| Dil şartı | 10 | İlan ileri düzey İngilizce (native/fluent, günlük İngilizce iletişim) şart koşuyorsa, profildeki "İngilizce — Temel" seviyesiyle risk oluşturur; puan düşürülür. Türkçe konuşulan/yerel operasyon ilanlarında bu madde tam puan alır. |
| Eğitim şartı | 5 | Lisans şartı varsa karşılanıyor (İşletme lisansı). Belirli bir alanda yüksek lisans/MBA *zorunlu* koşuluyorsa küçük bir düşüş uygulanır (elenmez, sadece not edilir). |
| Somut/ölçülebilir gereksinim eşleşmesi | 5 | İlan "SLA/KPI kurulumu", "outsource/vendor yönetimi", "AI destekli IVR" gibi somut proje deneyimi istiyorsa, `profil.md > Anahtar Başarılar` ile doğrudan örtüşüyor mu? |

## Eşik Değerleri

- **≥ 70 puan → Başvur.** `apply` becerisi CV'yi ve ön yazıyı ilana göre
  kişiselleştirip taslak hazırlar.
- **50–69 puan → Koşullu.** Kullanıcıya kısa bir gerekçeyle sun ("Bu ilan
  orta düzeyde uyumlu; eksik nokta: ..."); kullanıcı onaylarsa devam et.
- **< 50 puan → Başvurma, ilanı listeden çıkar** ve nedenini
  `scrape` çıktısındaki not alanına yaz (ör. "İngilizce fluent şartı,
  profil temel seviyede").

## ATS Format Uyumluluğu Notları

`cv-sablonu.tex`'ten üretilen PDF, insana yönelik iki sütunlu bir tasarımdır.
Bazı eski ATS ayrıştırıcıları (parser) çok sütunlu PDF'lerde metin sırasını
karıştırabilir. Bu nedenle:

- İlan doğrudan bir ATS portalına (Workday, Lever, Greenhouse vb.) yükleme
  istiyorsa, `apply` becerisi PDF'in yanında **düz metin / tek sütun**
  bir CV özeti de üretmeyi teklif etmelidir (ATS güvenliği için).
- İlan yalnızca e-posta veya LinkedIn "Kolay Başvuru" üzerinden başvuru
  istiyorsa, iki sütunlu PDF doğrudan kullanılabilir (insan tarafından
  okunacaktır).

## Değerlendirme Sırasında Yapılmayacaklar

- Rakamları/unvanları/tarihleri **abartmak veya uydurmak** yasak —
  `profil.md`'deki olgusal içerik değiştirilmez, sadece vurgu/sıralama
  değişir.
- Puan eşiğinin altındaki ilanlara "belki olur" diyerek başvuru taslağı
  üretilmez; kullanıcı zamanı korunur.
- Aynı ilana birden fazla kez başvuru taslağı üretilmez (bkz. `scrape`
  ve `apply` becerilerindeki başvuru takip günlüğü).
