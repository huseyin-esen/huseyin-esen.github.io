# Proje: huseyin-esen.github.io

Hüseyin Esen'in (Kocaeli Üniversitesi, Kimya) akademik sitesi. Jekyll +
AcademicPages (Minimal Mistakes tabanlı).

- Canlı site: <https://huseyin-esen.github.io>
- Depo: `huseyin-esen/huseyin-esen.github.io`
- **Önce oku:** `docs/calisma-gunlugu.md` — her kararın gerekçesi ve bekleyen
  işler orada. Bu dosya yalnızca çalışma kurallarını tutar.

## Dil

- Kullanıcıyla **Türkçe** konuş.
- Sitenin yayına giden içeriği, kod yorumları ve commit mesajları **İngilizce**.

## Çalışma akışı

- `develop` üzerinde çalış → `master`'a fast-forward → ikisini de push et.
  GitHub Actions (`.github/workflows/pages.yml`) yalnızca `master`'ı yayınlar.
- Rutin değişikliklerde **onay sormadan** commit + push et, sonra canlı adresi
  build bitene kadar sorgulayıp sonucu doğrula (deploy 1–4 dk sürer).
  Sunucu HTML/CSS'i 10 dk önbelleğe alır: kullanıcı değişikliği görmüyorsa
  Ctrl+F5 öner.
- İzin **dışında** kalanlar: force push, geçmişi yeniden yazma, ve kullanıcının
  bilgisi olan veriyi (patent no, künye, özet metni) uydurmak — bunlarda sor.
- Ruby yok, Jekyll yerelde derlenmez. YAML'ı ve Liquid `if/for/case/comment`
  dengesini Python ile doğrula, sonucu canlı sitede kontrol et.
- `compress_html` satır içi script'leri tek satıra indirir: script içinde
  `//` yorum **kullanma**, yalnızca `/* */`.

## Yayın kayıtları (`_publications/`)

Front matter alanları: `title`, `category` (`manuscripts` / `books` /
`conferences`), `permalink` (`/publication/<ad>`), `date`, `authors`
(`"Soyad, A."` listesi), `venue`, `volume`, `issue`, `pages`, `doi`,
`paperurl`, `citation`, `excerpt`, `abstract`, `graphical_abstract`,
`graphical_abstract_full`, `graphical_abstract_wide`.

- **Her DOI'yi Crossref'te doğrula** (`https://api.crossref.org/works/<doi>`):
  başlık, yazarlar, cilt/sayı/sayfa eşleşmeli. Sitede bir DOI bir kez başka
  birinin makalesine çıkmıştı.
- Cilt/sayı atanmışsa **basım yılını** kullan, çevrimiçi-önce tarihi değil.
- Dosya adındaki tarih `date` alanıyla aynı olmalı; permalink sabit kalır.
- Özetleri **kelime kelime** aktar. Yalnızca çıkarma artıklarını onar
  (birleşmiş kelimeler, satır kırılmaları, ligatürler) ve kaybolan biçimi geri
  koy (tür adları italik, `r<sub>st</sub>` gibi alt simgeler). Yazım hatası
  gibi görünenleri **düzeltme, kullanıcıya bildir**.
- Patentler `_patents/` koleksiyonunda; konferans posterleri `_talks/` içinde
  `category: posters` ile.

## Grafik özetler

- Pillow ile dönüştür: **900 px görünen** + **1800 px tam boy**
  (`graphical_abstract_full`, tıklanınca açılır).
- Düz renkli figür → 256 renkli PNG. JPEG yalnızca gradyan ağır figürde.
- Küçük kaynak görseli **asla büyütme**; kendi boyutunda bırak.
- 2:1'den geniş şerit figürlerde `graphical_abstract_wide: true`.
- **Bulut oturumunda kullanıcının bilgisayarındaki dosyalara erişilemez.**
  Kullanıcı kaynak görseli GitHub web arayüzünden `incoming/` klasörüne
  yüklesin (*Add file → Upload files*). Dönüştür, `images/` altına koy,
  `incoming/` içindeki kaynağı sil. `incoming/` yayına girmez.
- Sohbete eklenen bir görselin baytları diske yazılamaz; dosya olarak lazım.
- Pillow kurulu değilse: `pip install pillow`.
