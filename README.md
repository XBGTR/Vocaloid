# Işıl · Derinlerin Sesi

Tarayıcıda şarkı söyleyen, animasyonlu bir sanal vokalist. **Işıl**, Mariana Çukuru'nda yaşayan, şarkı söylemeye bayılan küçük bir fener balığı. Parmağını (ya da fareni) sonar alanında gezdirdikçe "aaa-ooo" diye şarkı söylüyor; ağzı sesin ünlüsüne ve şiddetine göre şekil değiştiriyor.

Hiçbir ses dosyası kullanılmıyor. Ses, Web Audio API ile **formant sentezi** kullanılarak gerçek zamanlı üretiliyor. Tüm site tek bir `index.html` dosyası; framework ya da kütüphane yok.

## Özellikler

- **Formant sentezi:** testere dişi, darbe, kare ya da "gırtlak" dalgası ile nefes gürültüsü, ardından 3 paralel bandpass formant filtresi. Ünlüler arasında yumuşak geçiş var.
- **Doğallık:** gecikmeli başlayan vibrato, yavaş rastgele perde kayması (jitter), genlik titreşimi, yumuşak saldırı/bırakma zarfı, tiz notalarda formant izleme.
- **Efektler:** ping-pong eko (geri beslemesi koyulaşan) ve yapay dürtü yanıtlı yankı (ConvolverNode).
- **XY alanı:** X perdeyi, Y ünlüyü (A-E-İ-O-U) belirler. Çoklu dokunuşta her parmak ayrı bir ses çıkarır.
- **Gama kilidi:** Serbest, Kromatik, Majör, Minör, Pentatonik ve Hicaz; kök nota seçilebilir.
- **Klavye:** `A W S E D F T G Y H U J K O L P Ş İ` tuşları piyano düzeninde. `Z`/`X` oktavı, `1`–`5` ünlüyü değiştirir. Ekran piyanosu da var.
- **Web MIDI:** nota (hıza duyarlı), modülasyon tekerleğiyle ünlü, pitch bend ile perde (Chrome/Edge).
- **6 ön ayar:** Işıl, Batık Tapınak Korosu, Sonar-9 (robot), Mercan Operası, Balina Ninnisi, Baloncuk. Her biri karakterin rengini de değiştirir.
- **WAV kaydı:** AudioWorklet ile kayıpsız 16-bit stereo WAV.
- **Sese tepki veren arka plan:** planktonlar, fenerden yayılan ses halkaları, ağızdan yükselen harfli baloncuklar.

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
  4. İstersen **Site configuration → Change site name** ile adresi `isil.netlify.app` gibi bir şeye çevirebilirsin.

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
        ├─► bandpass F2 ─► kazanç ─┼─► titreşim ─► zarf ─► pan ─► sesler
        └─► bandpass F3 ─► kazanç ─┘
vibrato LFO + jitter ─► osilatör.detune

sesler ─┬─────────────────────────► master ─► sınırlayıcı ─► hoparlör / WAV kaydı
        ├─► ping-pong eko ─┬──────► master
        │                  └─► yankı
        └─► yankı (Convolver) ────► master
```

Ayrıntılı açıklamalar `index.html` içindeki yorumlarda.
