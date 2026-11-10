# Sağlık Paneli

Melis ve Muhammed için kişisel laboratuvar sonucu takip paneli. Tek dosyalık HTML + düz JSON. Dış bağımlılık, build adımı, sunucu veya hesap gerekmez.

## Kurulum (GitHub Pages)

1. `index.html` ve `saglik-verileri.json` reponun **kök dizininde** durur
2. Repo → **Settings** → **Pages**
3. **Source**: `Deploy from a branch` · **Branch**: `main` · **Folder**: `/ (root)` → Save
4. 1–2 dakika sonra panel şu adreste yayında olur:
   `https://<kullanici-adin>.github.io/<repo-adi>/`

Bu linke laptop ve telefondan girilebilir. iPhone'da Safari'de aç → Paylaş → **Ana Ekrana Ekle** dersen uygulama gibi tam ekran açılır.

Repo private ise Pages, GitHub'ın ücretli planlarında çalışır. Sağlık verisi olduğu için repoyu private tutmak istersen ve Pages çalışmazsa: `index.html`'i telefona/bilgisayara indirip yerel açabilirsin — o durumda veriyi alt bardaki **Yükle** düğmesiyle verirsin.

## Kullanım

- **Kişi sekmeleri** — Muhammed / Melis arası geçiş
- **Değer kartı** — son sonuç, tarih, kaç zaman önce bakıldığı, referans aralığına göre konum çubuğu
- Karta dokun → **grafik + tüm geçmiş kayıtlar**
- 1 yıldan eski sonuçlar "1 yıldan eski" etiketi alır
- **Oto / Mobil / Masaüstü** — görünüm modu. Oto ekran genişliğine göre kendisi ayarlar; istersen elle sabitle
- **Yükle** — güncellenmiş JSON'u panele al
- **İndir** — mevcut veriyi JSON olarak indir
- **Özet** — tüm son değerleri panoya kopyalar; bir LLM'e yapıştırıp değerlendirme isteyebilirsin

Veri bu depodaki `saglik-verileri.json` dosyasındadır; panel açılırken onu okur, bakmak için token gerekmez. **Yükle** ile verilen JSON'un depoya da yazılması için o cihazda bir kez **GitHub yedeği** ekranından fine-grained token gir (sadece `saglik-paneli`, Contents: Read and write).

## Veri formatı

`saglik-verileri.json` tek doğruluk kaynağıdır ve kendi şemasını içinde taşır (`schema` alanı). Herhangi bir metin editörüyle veya herhangi bir LLM'e "şu şemaya göre şu sonucu ekle" diyerek düzenlenebilir. Panel özel bir formata bağımlı değildir.

```json
{
  "kisiler": {
    "muhammed": {
      "ad": "Muhammed",
      "majorler": ["vitamin_d", "tsh"],
      "markerlar": {
        "vitamin_d": {
          "etiket": "D Vitamini (25-OH)",
          "birim": "ng/mL",
          "refDusuk": 30,
          "refYuksek": 80,
          "sonuclar": [
            { "tarih": "2026-02-02", "deger": 25.5, "lab": "", "not": "" }
          ]
        }
      },
      "kilo":   [{ "tarih": "2026-07-27", "deger": 78.4 }],
      "notlar": [{ "tarih": "2026-07-27", "metin": "D vitamini kürü başladı" }]
    }
  }
}
```

Kurallar:

| Alan | Kural |
|---|---|
| `tarih` | ISO 8601 string, `YYYY-MM-DD` |
| `deger` | JSON number. `25.5` doğru, `"25,5"` yanlış |
| marker anahtarı | küçük harf + alt çizgi: `vitamin_d`, `tsh` |
| `refDusuk` / `refYuksek` | marker seviyesinde varsayılan; `sonuclar[]` içinde verilirse o ölçüm için geçerli olur |
| `majorler` | panelde üstte gösterilecek marker anahtarları. Listede olmayan markerlar "Diğer Değerler" altında görünür |

`sonuclar` dizisinin sıralı olması gerekmez; panel tarihe göre kendisi sıralar. Geçersiz tarih veya sayı içeren kayıtlar sessizce atlanır, panel çökmez.

## Yeni sonuç ekleme

1. Yeni tahlil çıktığında ilgili markerın `sonuclar` dizisine bir nesne ekle
2. Laboratuvarın referans aralığı farklıysa o kayda `refDusuk` / `refYuksek` yaz
3. `meta.sonGuncelleme` alanını güncelle
4. Push et — panel otomatik güncellenir

Marker mevcut değilse `markerlar` altına yeni anahtar aç ve `majorler` listesine eklemeyi unutma.

## Not

Bu panel kişisel takip ve görselleştirme aracıdır; tıbbi teşhis veya tedavi önerisi değildir. Renklendirme yalnızca girilen referans aralığına göre yapılır. Yorum ve doz kararları için hekime danışın.
