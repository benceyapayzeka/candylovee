# Video 2

## Video Bilgisi

**Başlık:** Puzzle Oyunu Yapıyorum — Claude Code, DeepSeek ve 6 Paralel Agent  
**Link:** —  
**İzlenme Tarihi:** 2026-11-06  
**Oynatma Listesi:** Yapay Zekaya Sıfırdan Başla (Sırayla İzle)

---

## Ana Kavramlar

- **Git repo her şeyden önce gelir** — Agent takibi ve geri alma (rollback) için projeye başlar başlamaz `git init` şart.
- **CLAUDE.md = sürekli context enjeksiyonu** — Bu dosyadaki her şey modelin context window'una otomatik giriyor. Ama her şeyi buraya doldurmak yanlış; büyük dosyalar için sadece *adres* ekle, model gerektiğinde okusun.
- **rules.md ile sınır koy** — Agent'lara neyi *yapmamaları* gerektiğini söyleyen kural dosyası. Minimum 3 kural: (1) belirli ilerleme sonrası commit at, (2) scope'ları ayır ki aynı işi iki agent yapmasın, (3) commit atmadan önce testini geç.
- **Model seçimi = maliyet/kalite tradeoff'u** — Opus/Max her iş için gereksiz. Basit frontend için düşük mod yeterli; ağır mimari kararlar için yüksek model. Gerçek hayatta kullanılabilirlik açısından DeepSeek gibi ucuz modelleri test etmek değerli.
- **Ping/pong triki** — Modele "bundan sonra ping'e pong de" dersen model hemen harekete geçmez, bekler. Aceleyle başlamak ileride büyük sorun yaratır; önce düşün, sonra yap.
- **Context sıfırlama (slash new)** — Artık gereksiz olan context'i taşımak hem yavaşlatır hem para yakar. Yeni bir aşamaya geçince context'i sıfırla.
- **6 paralel agent + /goal** — Aynı anda birden fazla agent fırlat; model hepsini takip eder, hata çıkarsa kendi retry eder. Sen sadece mimari kararı ver, iş kendisi döner. `/goal` aktif olunca sen artık müdahale etmeden görev tamamlanır.
- **Context caching** — Model aynı mesaj geçmişini her seferinde *baştan okumaz*; cache'den çeker. Claude Code kullanımının ~%97'si cache'den geliyor. Cache olmasaydı abonelik 30x daha hızlı biterdi. DeepSeek'te caching altyapısını doğru kurmak ~10-50x maliyet farkı yaratabilir.
- **Asset "fabrikası" mantığı** — Tekrar yapılacak işler için altyapı kur (görsel kesme, arka plan silme vb.), her seferinde sıfırdan yapma. Bir kere yap, sürekli kullan.
- **PRD / araştırma önce, kod sonra** — Model internette araştırma yapıp rapor üretebilir. Bu rapor CLAUDE.md veya /reports klasörüne eklenir; diğer agent'lar da aynı veriyi kullanır. Kod yazmadan önce modelle "ping/pong modunda" fikir alışverişi yap.
- **Sen mimar ol, model işçi** — "AI insanı aptallaştırır" argümanına yanıt: eğer sen mimari kararları veriyorsan, düşünmekten vazgeçmiyorsan aptallaşmazsın. Düşünce sistemini bir üst seviyeye atarsın. Modeli araç olarak kullan, düşünmeyi ona bırakma.
- **Mükis (MUQS) prensibi** — %20 emekle %80 sonuç al. Basit tut. Karmaşıklaştırma.

---

## Videodaki Uygulama Adımları (Transkrip Özeti)

Kullanıcı bu adımları video ile birlikte uygulamaya çalışıyor. Bir sonraki oturumda buradan devam et.

### Kurulum Aşaması
1. **tmux ile 4 bölümlü ekran** — Her bölümde ayrı terminal, her terminalde `claude` komutuyla Claude Code açıldı
2. **Effort → medium** — 4 terminalin hepsinde `/model` veya config üzerinden effort medium'a alındı (hızlı cevap için)
3. **Proje klasörü oluştur** — Videoda "gipsik" adıyla geçici klasör açıldı; `cd` ile içine girildi
4. **Git repo** — `git init` — rollback ve agent takibi için zorunlu
5. **.gitignore oluştur** — `.env` ve gereksiz dosyaları dışarıda tut
6. **CLAUDE.md oluştur** — Proje context'i için; her şeyi doldurmak yerine büyük dosyaların sadece *adresini* ekle
7. **DeepSeek API key** — `.env` dosyasına eklendi; Claude'a "keychain'de DeepSeek key var mı?" diye soruldu, varsa `.env`'e eklet
8. **Model slug → CLAUDE.md** — `deepseek-chat` (V4 Pro) model adını CLAUDE.md'ye eklet ki diğer agent'lar da kullansın

### Araştırma Aşaması (PRD önce, kod sonra)
9. **Ping/pong modu** — Yeni context açınca modele: *"Bundan sonra ping'e pong de, hemen harekete geçme"* — model bekler, acele etmez
10. **Araştırma görevi** — Bir agent'a: "Candy Crush tarzı oyunları iyi yapan nedir, araştır" → sonucu `/reports` klasörüne ekle
11. **Lovable araştırması** — Ayrı agent: "Lovable UI/UX'ini iyi yapan nedir?" → Bolt ve v0 da karşılaştırmalı araştırıldı
12. **Araştırma → CLAUDE.md** — Model sonuçları CLAUDE.md'ye ekledi; diğer agent'lar aynı veriyi okuyabildi
13. **Karşılıklı fikir alışverişi** — `/new` ile context sıfırla → ping/pong modunda modelle konuş → kararları netleştir → sonra kod

### rules.md Kurulumu
14. **rules.md oluştur** — CLAUDE.md'den bağlantılandır; 3 temel kural:
    - Belli ilerleme sonrası commit at
    - Scope'ları ayır (aynı işi iki agent yapmasın)
    - Commit atmadan önce test geçmeli
15. **CLAUDE.md'ye adres ekle** — `rules.md`'nin tam yolunu CLAUDE.md'ye ekle; model okumak zorunda kalacağını bilsin

### Asset Fabrikası
16. **Asset planı** — Modelle konuşarak kaç farklı asset gerektiğine karar ver (tema, background, board frame sayıları)
17. **6 paralel Codex agent** — Görsel üretim için: `aynı anda 6 Codex Image agent fırlat` → model hepsini takip eder, hata çıkarsa retry eder
18. **Arka plan silme altyapısı** — Görsellerin arka planını silecek script bir kere yaz, her seferinde sıfırdan yapma
19. **Manifest dosyası** — Her görselin adı ve açıklaması manifest'e eklensin; DeepSeek hangi görselin ne olduğunu bilsin
20. **/goal ile otomasyon** — Slash goal yaz → görev tamamlanana kadar model kendi kendine çalışır, sen müdahale etmezsin

### Context Sıfırlama Zamanları
- Araştırma bitti, koda geçiyorsun → `/new`
- Bir aşama kapandı, yenisine başlıyorsun → `/new`
- Context gereksiz büyüdü, yavaşladı → `/new`

### Agentik Mimari (DeepSeek için)
21. **DeepSeek context caching** — Araştır ve `.env`/config'e ekle; minimum cacheable token sayısını öğren
22. **İki tip görev** — Hafif/hızlı işler → DeepSeek Flash; ağır/kod tabanlı işler → DeepSeek Pro
23. **Kaliteli tool'lar** — DeepSeek Opus seviyesi çıktı veremez ama kaliteli sistem prompt + tool verirsen farkı kapatırsın; tool'ları Opus ile yaz, DeepSeek ile çalıştır

---

## Pratik Yaptıklarım

*(Kullanıcının bizzat denediği şeyler)*

- 

---

## Sorular / Anlamadığım Yerler

- Hooks tam olarak ne yapıyor? (Videoda "ileride hooks videosu yaparız" dedi — takip et)
- DeepSeek caching API'sini nasıl aktif ederim, minimum cacheable token sayısı nedir?
- `/goal` komutu tam olarak nasıl çalışıyor, hangi şartlarda durur?
- tmux ile aynı anda 4 agent penceresi — bu Claude Code'da mı, terminalde mi yönetiliyor?

---

## Bir Sonraki Videoya Notlar

- Hooks konusu geliyor — Claude Code hook'larının ne işe yaradığını öğreneceğiz
- Agentik mimari (sistem prompt + tool tasarımı) daha derin işlenecek
- DeepSeek ile Opus seviyesi çıktı almak için tool kalitesi kritik — bunu takip et
- Context caching pratikte nasıl kurulur, kendi projende dene
