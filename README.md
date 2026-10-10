# Oyun Korosu

Tarayıcıda şarkı söyleyen, animasyonlu bir sanal vokal grubu. Kadroda oyun ve animasyon dünyasının ünlü karakterleri var: **Şimşek McQueen** (Cars), **Sans** (Undertale), **Hornet** (Hollow Knight), **Sonic**, **Pikachu**, **Kirby**, **Creeper** (Minecraft) ve **Pac-Man**. Her karakteri ayrı ayrı şarkı söyletebilir, nota dizip döngü kaydederek hepsini aynı şarkıda birleştirebilirsin.

Hiçbir ses dosyası kullanılmıyor. Sesler, Web Audio API ile **formant sentezi** kullanılarak gerçek zamanlı üretiliyor. Tüm site tek bir `index.html` dosyası; framework ya da kütüphane yok (yalnızca başlıklar için Google Fonts'tan "Pixelify Sans" yazı tipi).

> Hayran yapımı, ticari olmayan bir projedir; resmî değildir. Karakterler ve isimleri sahiplerine aittir: Disney / Pixar, Toby Fox, Team Cherry, SEGA, The Pokémon Company / Nintendo, Nintendo / HAL Laboratory, Mojang / Microsoft, Bandai Namco.

## Özellikler

- **8 karakterlik koro:** Her karakter SVG ile çizildi. Ağızlar kendi kanallarının sesine ve ünlüsüne göre oynar; göz kırpma, nefes alma, sesle sallanma var. Karaktere özel ayrıntılar:
  - McQueen'in gözleri ön camda, ağzı tamponda; sesle kasası titreyip yaylanır.
  - Hornet oyundaki gibi ağızsızdır; hece gözlerinin biçiminden belli olur, pelerini ve ipek ipi sesle dalgalanır.
  - Sans'ın gözü parlar, Pikachu'nun yanakları kıvılcım saçar.
  - Creeper gür seste yanıp söner, Pac-Man'in ağız açısı ünlüye göre değişir.
- **Herkesin kendine özgü sesi (imza):** Karakterler aynı formant motorunu kullanır ama her birine sesleri karışmasın diye bir imza eklendi:
  - **McQueen:** 31 Hz'lik "motor gürlemesi" (genlik modülasyonu); notaya aşağıdan "vroom" diye kayarak girer.
  - **Sans:** Kuru ve pes; kare dalgalı 23 Hz modülasyonla kemik tıkırtısı, vibrato yok.
  - **Hornet:** Çok nefesli ve kısa; notaya yukarıdan düşerek girer ("Shaw!").
  - **Sonic:** Üç hafif akortsuz osilatörle kalın, parlak ve kendinden emin.
  - **Pikachu:** Formantları çok yukarıda, hızlı titrek vibrato, "pika!" düşüşü.
  - **Kirby:** Koyu, yuvarlak tını, aşağıdan "poyo" yükselişi, yavaş titreme.
  - **Creeper:** Çoğunlukla fitil gibi tıslama; arkada kısık bir vızıltı.
  - **Pac-Man:** 8-bit kare dalga, bit indirgeme cızırtısı ve "vaka-vaka" formant salınımı.
- **Karakter kanalları:** Her karakterin ayrı ses seviyesi, eko ve yankı gönderimi, sessiz (M) ve solo (S) düğmesi var.
- **Stüdyo:**
  - Çal/Dur, tempo (60–180 BPM), 1/2/4 ölçülük döngü ve metronom.
  - Her karakter için nota ızgarası (piano roll). Notanın hecesi (A-E-İ-O-U) ve uzunluğu seçilebilir; diğer karakterlerin notaları soluk görünür.
  - Döngü kaydı: XY alanında, klavyede ya da MIDI'de çaldığını bir tur boyunca kaydedip tekrar tekrar çalar. Karakter değiştirip katman ekleyebilirsin.
  - Demo şarkı (La minör, Am–F–C–G), çalışmanın tarayıcıda otomatik saklanması ve şarkıyı iki tur çalıp WAV olarak kaydetme.
- **Formant sentezi:** Testere dişi, darbe, kare ya da "gırtlak" dalgası ile nefes gürültüsü, ardından 3 paralel bandpass formant filtresi. Ünlüler arasında yumuşak geçiş, gecikmeli vibrato, jitter, saldırı/bırakma zarfı ve tiz notalarda formant izleme var.
- **Efektler:** Ping-pong eko (tempoya uydurulabilir) ve yapay dürtü yanıtlı yankı (ConvolverNode).
- **XY alanı:** X perdeyi, Y ünlüyü belirler. Çoklu dokunuşta her parmak ayrı bir ses çıkarır.
- **Gama kilidi:** Serbest, Kromatik, Majör, Minör, Pentatonik ve Hicaz; kök nota seçilebilir. Nota ızgarası da gama uyar.
- **Klavye ve MIDI:**
  - `A W S E D F T G Y H U J K O L P Ş İ` tuşları piyano düzenindedir; `Z`/`X` oktavı, `1`–`5` heceyi değiştirir.
  - `Boşluk` çalar/durdurur, `R` döngü kaydını açar, `←`/`→` karakter değiştirir.
  - Web MIDI desteklenir: hıza duyarlı notalar, modülasyon tekerleğiyle hece seçimi, pitch bend.
- **Sese tepki veren konser salonu:** Her karakter için spot ışığı, piksel konfetiler ve ekolayzer.

## Yerelde çalıştırma

`index.html`'i çift tıklayıp doğrudan açmak da çalışır. Bu durumda kayıt, daha eski ScriptProcessor yöntemine geçer. En iyi sonuç için (AudioWorklet ile kayıt) yerel bir sunucu kullan:

```bash
# Python ile
python3 -m http.server 8080
# veya Node ile
npx http-server -p 8080
```

Ardından tarayıcıda `http://localhost:8080` adresini aç.

## Ücretsiz yayına alma

Site tek bir statik dosyadan oluştuğu için derleme adımı gerekmez. Aşağıdaki üç yoldan birini seçmen yeterli.

### 1) GitHub Pages (önerilen)

1. Kodu GitHub'daki deponun ana dalına (`main`) birleştir. Örneğin bu dalın PR'ını onaylayıp birleştirebilirsin. `index.html` deponun **kök dizininde** olmalı.
2. GitHub'da depoyu aç ve **Settings → Pages** bölümüne git.
3. **Build and deployment → Source** altında **Deploy from a branch** seç.
4. **Branch** olarak `main`, klasör olarak `/ (root)` seçip **Save**'e bas.
5. 1–2 dakika sonra sayfanın üstünde adres görünür: `https://<kullanıcı-adın>.github.io/<depo-adı>/`
6. Her `git push`'tan sonra site kendiliğinden güncellenir.

> Not: GitHub'ın ücretsiz planında Pages yalnızca **herkese açık** depolarda çalışır. Depon özelse ya herkese açık yap ya da aşağıdaki Netlify/Vercel yollarından birini kullan.

### 2) Netlify

- **En hızlısı (sürükle-bırak):** <https://app.netlify.com/drop> adresine git ve `index.html`'in bulunduğu klasörü sayfaya sürükle. Saniyeler içinde `https://rastgele-ad.netlify.app` gibi bir adres alırsın.
- **GitHub'a bağlı (otomatik güncellenen):**
  1. Netlify'da **Add new site → Import an existing project → GitHub** seç ve depoyu yetkilendir.
  2. **Build command**'ı boş bırak, **Publish directory** olarak `/` (ya da `.`) yaz.
  3. **Deploy**'a bas. Bundan sonra her push otomatik yayına çıkar.
  4. İstersen **Site configuration → Change site name** ile adresi `oyun-korosu.netlify.app` gibi bir şeye çevirebilirsin.

### 3) Vercel

1. <https://vercel.com> adresine GitHub hesabınla giriş yap.
2. **Add New… → Project** deyip depoyu **Import** et.
3. **Framework Preset: Other** seç. Build ayarlarını boş bırak.
4. **Deploy**'a bas. Adresin `https://<proje>.vercel.app` olur.

### Yayından sonra kontrol listesi

- Site **HTTPS** üzerinden sunulmalı. Üç servis de bunu otomatik yapar. AudioWorklet (kayıt) ve Web MIDI yalnızca güvenli bağlamda çalışır.
- Telefonda ses gelmiyorsa telefonun sessiz modunu kapat ve sesi aç. iOS 17 ve sonrasında site sessiz düğmesini de aşmaya çalışır.
- MIDI için Chrome ya da Edge kullan. Tarayıcı izin isteyince onay ver.

## Ses motoru nasıl çalışıyor? (kısaca)

```
osilatör(ler) + nefes gürültüsü
        │
        ├─► bandpass F1 ─► kazanç ─┐
        ├─► bandpass F2 ─► kazanç ─┼─► titreşim ─► zarf ─► pan ─► karakter kanalı
        └─► bandpass F3 ─► kazanç ─┘
vibrato LFO + jitter ─► osilatör.detune

karakter kanalı (seviye, M/S) ─┬────────────────────────► master ─► sınırlayıcı ─► hoparlör / WAV kaydı
                               ├─► eko gönderimi ─► ping-pong eko ─┬─► master
                               │                                   └─► yankı
                               └─► yankı gönderimi ─► Convolver ──────► master
```

Sıralayıcı notaları tarayıcının ses saatine göre yaklaşık 120 ms ileriden zamanlar (25 ms'de bir çalışan zamanlayıcı). Böylece animasyon takılsa bile ritim kaymaz. Döngü kayıtları turun yüzdesi olarak saklanır; tempo değişince birlikte hızlanır ya da yavaşlar.

Ayrıntılı açıklamalar `index.html` içindeki yorumlarda.
