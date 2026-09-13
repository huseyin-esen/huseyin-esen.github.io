# Çalışma günlüğü — 13 Eylül 2026

Bu dosya, sitede yapılan değişikliklerin ve **neden öyle yapıldığının** kaydıdır.
Amaç: aradan zaman geçtiğinde "burayı neden böyle kurmuştuk?" sorusuna
bakılacak tek yer olmak. Commit mesajları neyin değiştiğini anlatır; burada
kararların gerekçesi var.

Site: <https://huseyin-esen.github.io> · Depo: `huseyin-esen/huseyin-esen.github.io`
Dağıtım: `master`'a push → GitHub Actions (`.github/workflows/pages.yml`) → yayın.
`develop` üzerinde çalışılıp `master`'a fast-forward ediliyor.

Ham sohbet kaydı (7 MB, JSONL) Claude Code tarafından şurada tutuluyor:
`~/.claude/projects/c--git-huseyin-esen-github-io/37be3851-cee4-4ac5-a0ab-b280ffd5c46d.jsonl`

---

## 1. Temizlik ve metadata düzeltmeleri

`f0c1d61`

- `_config.yml` içindeki `repository` alanı `huseyin-esen/huseyin.esen.github.io`
  yazıyordu, gerçek depo adı `huseyin-esen.github.io`. Düzeltildi.
- `bluesky: "bsky.app"` şablon placeholder'ıydı ve kenar çubuğunda bozuk bir
  bağlantı üretiyordu. Boşaltıldı.
- `scrape_talks.yml` iş akışı `talks/**` yolunu izliyordu, ama koleksiyon
  `_talks/`. Yani iş akışı hiç tetiklenmiyordu. Yol düzeltildi, action
  sürümleri güncellendi ve `permissions: contents: write` eklendi — iş sonunda
  `git push` yapıyor, varsayılan salt-okunur token'la o adım hata verirdi.
- İki yayın dosyasının adı içindeki tarihle uyuşmuyordu
  (`2010-01-01-...` içinde `date: 2013-01-01`). Dosyalar yeniden adlandırıldı;
  permalink'ler front matter'da sabit olduğu için URL'ler değişmedi.
- Ana sayfadan "Selected Publications" ve "Patents" bölümleri kaldırıldı
  (`f0c1d61` içinde) — ikisinin de kendi sayfası var.

**Not:** `news.md` içindeki "March 2026" tarihi ilk bakışta eski görünüyordu
ama doğruydu; site gerçekten o tarihte yayına alınmış. Tarih değil, "yeni
yayınlandı" tonu güncellendi.

---

## 2. Publications bölümünün yapısı

`8ba1005` → `ec160c8` → `ee41753` → `3f9f1f8`

Tek uzun liste yerine sekmeli yapı kuruldu. **Önce JavaScript ile sekme
paneli denendi (`8ba1005`), sonra vazgeçildi (`ec160c8`):** panel değişince
sayfa yüksekliği 10 kayıttan 1 kayda düşüyor ve aşağıda olan kaydırma konumu
sayfanın sonuna denk geliyordu. Her sekme gerçek bir sayfa olunca bu sorun
kökten kalktı.

Şu anki sayfalar:

| Sekme | Adres | Kaynak |
|---|---|---|
| Journal Articles | `/publications/` | `category: manuscripts` |
| Books | `/publications/books/` | `category: books` |
| Conference Papers | `/publications/conferences/` | `category: conferences` |
| Patents | `/publications/patents/` | `_patents` koleksiyonu |

Sekme çubuğu `_includes/publication-tabs.html`, stil
`_sass/layout/_section-tabs.scss`. Sekmeler `publication_category` verisinden
üretiliyor ve **boş kategorinin sekmesi çıkmıyor** — yeni bir kategoriye
yayın eklendiğinde sekmesi kendiliğinden oluşur.

Patents ayrı bir koleksiyon (`_patents/`), çünkü patentlerin dergisi, cildi
ve DOI'si yok; yayın koleksiyonuna koymak boş künye alanları ve anlamsız atıf
düğmeleri üretirdi. Her patentin `/patent/<ad>` adresinde kendi sayfası var —
bu, başlıklarının yayın başlıklarıyla **aynı bağlantı rengini** alması için
gerekliydi (`6a364ae`).

---

## 3. Atıf dışa aktarımı (BibTeX / RIS / EndNote)

`9974fa3` → `a3718ce` → `4821821` → `5e8644a`

Her yayının altında üç indirme düğmesi var; dosya tarayıcıda üretiliyor
(`_includes/citation-export-script.html`), sunucuda 36 ayrı dosya tutulmuyor.

**Bunun için 12 yayının hepsine `authors` listesi eklendi.** Önceden yazarlar
yalnızca `citation` metninin içinde gömülüydü, yapısal veri yoktu; doğru
BibTeX/RIS üretmek mümkün değildi. Bu alan sonradan grafik özet kartında da
işe yaradı.

Kararlar:

- EndNote için RIS'i tekrar kullanmak yerine EndNote'un kendi etiketli
  ("refer") formatı üretiliyor — yayıncıların "Export citation → EndNote"
  seçeneğinin verdiği format bu.
- Kayıt türleri kategoriye göre eşleniyor: makale `@article`/`JOUR`/
  `Journal Article`, kitap bölümü `@incollection`/`CHAP`/`Book Section`,
  bildiri `@inproceedings`/`CONF`/`Conference Paper`.
- BibTeX metin alanlarında LaTeX'e özel karakterler (`& % $ # _`) kaçışlanıyor,
  **DOI ve URL alanlarında kaçışlanmıyor** — ters eğik çizgi adresi bozar.
- Kitap bölümünün yayıncısı `venue` alanının içindeydi; ayrı `publisher`
  alanına taşındı. Görünür yan etkisi: o kaydın satırı artık parantezli
  yayıncı adı olmadan görünüyor.
- İkon renkleri sitenin kendi paletinden (`$warning-color`, `$success-color`,
  `$danger-color`) türetiliyor. Kontrast ölçüldü: üçü de ikonlar için gereken
  3:1 eşiğini geçiyor (amber 3.20:1, yeşil 4.06:1, kırmızı 3.95:1). Amber
  başta 2.93:1 ile eşiğin altındaydı, karartma %12'den %16'ya çıkarıldı.

**Test edilmeyen kısım:** Bu ortamda tarayıcı olmadığı için butona tıklayıp
dosyanın indiğini doğrulayamadım. Dosya içeriğini üreten mantık Python'da
birebir çalıştırılıp çıktı kontrol edildi; indirme adımı kullanıcı tarafında
test edilmeli.

---

## 4. Yayın listesi görünümü: grafik özet kartı

`c7af0cf`

Referans olarak bir dergi kartı düzeni paylaşıldı (ACS Macro Letters kartı).
Uygulandı: sol kenarda dikey DOI rayı, ortalanmış başlık bandı (italik
başlık + kalın dergi), sola yerleşen grafik özet ve etrafına sarılan iki yana
yaslı özet metni.

- Kart `_includes/archive-single-publication.html`, stil
  `_sass/layout/_publication-card.scss`.
- **Paylaşılan `archive-single.html` dosyasına dokunulmadı** — onu talks,
  teaching ve portfolio da kullanıyor. Kart yalnızca üç Publications
  sayfasına bağlandı.
- Yazar adları `"Esen, H."` biçiminden referanstaki `"H. Esen"` biçimine
  Liquid içinde çevriliyor.
- **Kart eksik veriyle çökmüyor:** grafik özet yoksa metin tam genişlikte
  akar, tam özet yoksa `excerpt` yerine geçer, DOI yoksa ray boş kalır.

Referansta yazar adları kalın değildi, o yüzden kalınlaştırma eklenmedi.
İstenirse tek satır.

---

## 5. Conferences bölümü

`77d2840` → `866e56d` → `8a34ed8`

"Talks" menü öğesi **"Conferences"** olarak değiştirildi ve iki sekmeye
bölündü:

| Sekme | Adres |
|---|---|
| Talks | `/conferences/` |
| Posters | `/conferences/posters/` |

Sekme çubuğu `_includes/conference-tabs.html`. **Posters sekmesi boş olsa da
görünüyor** — Publications'ın tersine, burada kalıcı bir bölüm olması
istendi.

Sınıflandırma mantığı: **bir kayıt, aksini söylemedikçe talk sayılır.** Bir
kaydı postere taşımak için `_talks/` içindeki dosyaya tek satır:

```yaml
category: posters
```

Böylece mevcut dört kayda hiç dokunulmadı.

Bu bölümde bir ara çözüm denenip terk edildi: iki bölüm tek sayfada `<h2>`
alt başlıkları olarak duruyordu (`866e56d`), ama boş Posters başlığı sayfanın
en dibinde gözden kaçıyordu. Sekmeye dönüştürülünce sorun kalmadı.

**Dikkat — isim benzerliği:** Sitede iki ayrı "conference" var.
`/conferences/` katıldığı konferanslar (sunum ve poster),
`/publications/conferences/` ise yayınlanmış konferans bildirisi. İkisi
farklı şeyler, karıştırmamak gerek.

---

## 6. Yayınlara eklenenler

| Yayın | DOI | Commit |
|---|---|---|
| Lichen-Derived Mineral Residue... (Polymer Composites, 2026) | `10.1002/pc.71612` | `9bc829d` |
| Utilization of renewable filler from lichen in LDPE (Polymer Composites, 38(2)) | `10.1002/pc.23597` | `ee41753` |
| Residual stresses in injection molded shape memory polymer parts (AIP Conf. Proc. 1713) | `10.1063/1.4942338` | `ee41753` |

Metadata **Crossref API'den doğrulandı**, PDF'ten okunup varsayılmadı.

İki tarih kararı:

- 2026 makalesi **Early View**: cilt/sayı/sayfa henüz atanmamış. Bu alanlar
  bilinçli olarak boş bırakıldı — PDF başlığındaki geçici `0:1–16`
  sayfalamasını girmek BibTeX/RIS çıktısına yanlış sayfa yazardı. Sayıya
  atandığında eklenmeli.
- LDPE makalesi Crossref'te çevrimiçi 2015-05-26, basım 2017-02. **2017**
  kullanıldı; hem cilt/sayı o yıla ait hem de 2026 makalenin kendi
  kaynakçasında 2017 olarak atıf verilmiş.

### Düzeltilen hatalı kayıt

`ee41753` — Sitede *"Light induced curing of Clay/polymer nanocomposites"*
başlıklı bir kayıt vardı: JAPS **103(1), 626-633**, Esen/Küsefoğlu/Wool.
Verilen `10.1002/app.25155` DOI'sinin künyesi **birebir aynı** çıktı: aynı
dergi, cilt, sayı, sayfa ve yazarlar — ama gerçek başlık *"Photolytic and
free-radical polymerization of **monomethyl maleate esters** of epoxidized
plant oil triglycerides"*.

İki makale aynı sayfaları paylaşamaz ve Crossref'te o clay/polymer
başlığıyla bu yazarlara ait bir kayıt yok. Kayıt gerçek başlık ve DOI ile
düzeltildi; eski adres yeni adrese yönlendiriliyor.

> **Doğrulanması gereken:** "Light induced curing of Clay/polymer
> nanocomposites" gerçekten ayrı bir yayınsa (Crossref'te indekslenmeyen bir
> dergi ya da bildiri olabilir), künyesi verilip ayrı kayıt olarak
> eklenmeli. Eski dosya git geçmişinde duruyor.

---

## 7. Site genelinde diğer değişiklikler

- **Dış bağlantılar yeni sekmede açılıyor** (`78bb05e`). `_includes/scripts.html`
  içindeki script tüm sayfalarda çalışıyor, Markdown içinde yazılmış
  bağlantılar dahil. Aynı alan adı, sayfa içi çapa, `mailto:`/`tel:` ve kendi
  `target`'ı olan bağlantılara dokunmuyor; `rel="noopener noreferrer"` ekliyor.
  **Sıkıştırma notu:** `compress_html` tüm script'i tek satıra indiriyor, bu
  yüzden bu dosyalarda `//` tarzı yorum kullanılmamalı — yalnızca `/* */`.
- **Altbilgi tamamen kaldırıldı** (`b62b4fa`). `page__footer` bloğu iki
  şablondan (`default.html`, `cv-layout.html`) içeriğiyle birlikte silindi;
  yalnızca include'ları boşaltmak sayfa altında boş gri bir şerit bırakırdı.
  Temanın altbilgi include dosyaları diskte kullanılmadan duruyor, geri
  eklemek her şablona beş satır. MIT lisans bildirimi `LICENSE` dosyasında
  olduğu için künyeyi sayfadan kaldırmak lisansı ihlal etmiyor.
  **Yan etki:** RSS akışının (`feed.xml`) görünür bağlantısı kalmadı.
- **Sitemap kaldırıldı** (`3e507c7`, `6df4f54`). Altbilgideki çıplak bağlantı
  ve `_pages/sitemap.md` sayfası silindi. `sitemap.xml` (jekyll-sitemap
  eklentisinin arama motorları için ürettiği dosya) korundu.
- **LinkedIn adresi yüzde-kodlandı** (`a17bd7e`). Profil adı `ü` içeriyor;
  ham karakterle bağlantının çalışması tarayıcının kodlamasına bırakılmıştı.
- **Publications başlığı altındaki Google Scholar cümlesi kaldırıldı**
  (`f556be7`). Bağlantı kenar çubuğunda ve Contact sayfasında duruyor.
- `.claude/settings.local.json` `.gitignore`'a eklendi — kişisel izin
  kuralları halka açık depoya gitmesin.

---

## Bekleyen işler

1. **Grafik özet görselleri ve tam özet metinleri.** Kartın tam etkisi için
   kayıt başına `graphical_abstract` (dosya `images/` altına) ve `abstract`
   alanları gerekiyor. Şu an yalnızca 2026 makalesinde gerçek özet var,
   hiçbirinde görsel yok. Alternatif: kartı yalnızca öne çıkan son birkaç
   makalede görselli kullanmak.
2. **2026 makalesinin cilt/sayı/sayfası**, dergi sayıya atadığında eklenmeli.
3. **Clay/polymer kaydının durumu** (bkz. bölüm 6).
4. **Patent numaraları ve tescil tarihleri** — patent detay sayfalarında şu an
   yalnızca başlık ve açıklama var, künye satırı yok.
5. **Şablon artıkları hâlâ yayında:** `_posts/` altındaki 5 lorem ipsum blog
   yazısı (RSS akışına da giriyor), `_portfolio/` (2 dosya),
   `_pages/markdown.md`, `terms.md`, `archive-layout-with-content.md`,
   `non-menu-page.md`, `_data/comments/` altındaki 7 örnek yorum ve `images/`
   içindeki şablon görselleri (`foo-bar-identity.jpg`, `image-alignment-*`
   vb.). Hiçbiri menüde görünmüyor ama adresleri açık.
6. **Talks/Teaching içerikleri yüzeysel.** Başlıklar gerçek ama açıklamalar
   birer paragraflık ve jenerik; mekân/tarih bilgileri doğrulanmalı.
