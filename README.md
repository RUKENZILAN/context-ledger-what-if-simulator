# Context://Ledger

**A token-budget & compression decision-support panel for LLM system engineers.**
*(English below · Türkçe metin aşağıdadır)*

---

## 🇬🇧 English

### What it is

Context://Ledger is a single-file, offline, interactive calculator that helps engineers reason about **context window usage, compression strategy, cost, and latency** when designing LLM-based systems (RAG pipelines, agents, long-context chat, etc.).

It treats the context window like a financial ledger: system prompt, conversation history, and retrieved (RAG) context are "debits" against a fixed token budget; reserved output is a "hold"; whatever's left is your balance. The panel shows that balance before and after applying a compression method, then translates the difference into cost and latency terms.

### ⚠️ Limitations — read this first

**This is a simulator, not a measurement tool.** Before using it for real planning, understand what it does *not* do:

- **No real tokenizer.** Token counts for system prompt, history, retrieved context, and output are numbers you type in by hand — they are not computed from actual text using a real tokenizer (e.g. tiktoken, Anthropic's counter). If your estimates are off, every downstream number is off too.
- **No live pricing.** Model pricing, throughput, and context-window sizes are hard-coded defaults baked into the tool, not fetched from any vendor API. They can go stale the moment a vendor changes pricing.
- **No real compression.** Selecting a compression method does not run any actual summarization, pruning, or LLMLingua-style compression on your content — it applies a fixed assumed ratio and a heuristic fidelity-loss estimate for that method.
- **No live network calls at all.** Everything runs locally in your browser with the numbers you provide; the tool cannot verify or correct any of your inputs.
- **Latency model is simplified.** `prefill time + generation time + fixed overhead` is a rough approximation useful for relative before/after comparisons, not a substitute for real load testing.

In short: treat every output as a **directional estimate for exploring trade-offs**, not a number to put in a contract, an SLA, or a finance report without independent verification.

### Features

- **The Ledger** — a segmented bar visualizing how system prompt / history / retrieved context / reserved output fill the model's context window, before and after compression, with an overflow warning and a "within budget / tight / over budget" stamp.
- **8 model presets** — GPT-4o, GPT-4o mini, Claude Sonnet 4.5, Claude Haiku 4.5, Gemini 1.5 Pro, Gemini 1.5 Flash, Llama 3.1 405B, plus a fully editable **Custom** preset (context window, input/output price per 1M tokens, throughput).
- **6 compression methods** — None, Deduplication, Extractive summarization, Embedding-based pruning, Abstractive (LLM) summarization, LLMLingua-style prompt compression — each with a typical ratio range and a heuristic fidelity estimate.
- **Compression Impact panel** — tokens saved, estimated fidelity retained, remaining headroom.
- **Cost Ledger** — per-request and monthly (30-day) cost, before vs. after compression, scaled by your requests/day.
- **Latency Estimate** — rough prefill + generation time before vs. after compression.
- **Rule-based recommendations** — plain-language guidance (e.g. "overflow, increase compression," "tight budget," "compression saves ~X% monthly cost," "fidelity may be too low for high-stakes tasks").
- **Bilingual UI** — toggle between English and Turkish at any time; all labels, units, and generated recommendations switch instantly, including locale-correct number/currency formatting.

### How to use it

1. Open `context-ledger.html` in any modern browser — no install, no server, no external API calls. All calculations run locally in JavaScript.
2. Pick a **model** (or choose *Custom* and enter your own window size, pricing, and throughput).
3. Enter your **system prompt**, **conversation history**, **retrieved/RAG context**, and **reserved output** token counts.
4. Pick a **compression method** and adjust the **ratio** slider (it defaults to that method's typical value).
5. Enter your expected **requests/day** to see monthly cost impact.
6. Read the **Ledger** bars, **Compression Impact**, **Cost Ledger**, **Latency Estimate**, and **Recommendation** panels — they all update live.

### Methodology notes (please read before using in production planning)

- **Pricing, throughput, and fidelity figures are illustrative planning estimates**, not live data pulled from any vendor. They are hard-coded defaults meant to be *directionally* useful and are fully editable via the *Custom* model. Always check the current pricing page of the vendor you actually plan to use.
- **Fidelity retained** is a simple heuristic (each compression method has a baseline fidelity that degrades slightly as you push the ratio above its typical range). It is **not** a benchmarked or measured quality score — validate real outputs before committing to a ratio for high-stakes or factual use cases.
- Compression in this tool is applied only to **history + retrieved context** (not the system prompt or reserved output), which reflects how most compression pipelines are actually deployed.
- Latency is a simplified `prefill_time + generation_time + fixed overhead` model, useful for relative before/after comparison — not a substitute for real load testing.

### Tech stack

Plain HTML, CSS, and vanilla JavaScript in a single self-contained file — no build step, no dependencies, no external network calls at runtime (Google Fonts is the only external asset, used purely for typography). Safe to host anywhere, embed, or hand to a client as a standalone deliverable.

### Customizing / rebranding

- Model presets, compression-method definitions, and all copy live near the top of the `<script>` block (`MODELS`, `ALGOS`, `ALGO_HINT`, `I18N`) — edit those objects to add models, change pricing, add languages, or adjust wording.
- Color roles are defined as CSS custom properties in `:root` (`--blue`, `--amber`, `--violet`, `--teal`, `--red`, etc.) for quick re-theming.

### License / usage

This tool and its source are provided as-is for internal, client, or commercial use as agreed with whoever commissioned it. No warranty is made regarding the accuracy of pricing, throughput, or fidelity assumptions — see *Methodology notes* above.

---

## 🇹🇷 Türkçe

### Nedir

Context://Ledger, LLM tabanlı sistemler (RAG hatları, ajanlar, uzun bağlamlı sohbet vb.) tasarlayan mühendislerin **bağlam penceresi kullanımı, sıkıştırma stratejisi, maliyet ve gecikme** konularında karar vermesine yardımcı olan, tek dosyalık, çevrimdışı çalışan, interaktif bir hesaplama aracıdır.

Araç, bağlam penceresini bir muhasebe defteri gibi ele alır: sistem istemi, konuşma geçmişi ve alınan (RAG) bağlam, sabit bir token bütçesine karşı "borç" kalemleridir; ayrılan çıktı bir "rezerv"dir; geriye kalan ise bakiyenizdir. Panel bu bakiyeyi bir sıkıştırma yöntemi uygulanmadan önce ve sonra gösterir, ardından aradaki farkı maliyet ve gecikme cinsinden yorumlar.

### ⚠️ Sınırlamalar — önce bunu okuyun

**Bu bir simülatördür, ölçüm aracı değildir.** Gerçek planlama için kullanmadan önce şunları bilin:

- **Gerçek tokenizer yok.** Sistem istemi, geçmiş, alınan bağlam ve çıktı için girdiğiniz token sayıları elle yazdığınız tahminlerdir — gerçek bir tokenizer (ör. tiktoken, Anthropic'in token sayacı) ile metinden hesaplanmaz. Tahmininiz yanlışsa, sonraki tüm sayılar da yanlış olur.
- **Canlı fiyatlandırma yok.** Model fiyatları, işlem hızı ve bağlam penceresi boyutları, araca sabit kodlanmış varsayılan değerlerdir; herhangi bir sağlayıcı API'sinden çekilmez. Sağlayıcı fiyat değiştirdiği an bu değerler güncelliğini yitirir.
- **Gerçek sıkıştırma yok.** Bir sıkıştırma yöntemi seçmek, içeriğiniz üzerinde gerçek bir özetleme, budama veya LLMLingua tarzı sıkıştırma çalıştırmaz — o yöntem için sabit varsayılan bir oran ve sezgisel bir doğruluk kaybı tahmini uygular.
- **Hiçbir canlı ağ çağrısı yok.** Her şey, sizin girdiğiniz sayılarla tarayıcınızda yerel olarak çalışır; araç girdilerinizi doğrulayamaz veya düzeltemez.
- **Gecikme modeli basitleştirilmiştir.** `ön işlem süresi + üretim süresi + sabit ek yük`, göreli önce/sonra karşılaştırmaları için kullanışlı kaba bir yaklaşımdır — gerçek yük testinin yerini tutmaz.

Kısacası: her çıktıyı, **ödünleşimleri (trade-off) keşfetmek için yön gösterici bir tahmin** olarak görün; bağımsız doğrulama yapmadan bir sözleşmeye, SLA'ya veya finansal rapora koyulacak bir sayı olarak değil.

### Özellikler

- **Defter** — sistem istemi / geçmiş / alınan bağlam / ayrılan çıktının modelin bağlam penceresini sıkıştırma öncesi ve sonrasında nasıl doldurduğunu gösteren segmentli bir çubuk; aşım uyarısı ve "bütçe içinde / sınırda / aşıldı" damgasıyla birlikte.
- **8 model şablonu** — GPT-4o, GPT-4o mini, Claude Sonnet 4.5, Claude Haiku 4.5, Gemini 1.5 Pro, Gemini 1.5 Flash, Llama 3.1 405B ve tamamen düzenlenebilir bir **Özel** şablon (bağlam penceresi, 1M token başına girdi/çıktı fiyatı, işlem hızı).
- **6 sıkıştırma yöntemi** — Yok, Yinelenen İçerik Temizliği, Çıkarımsal Özetleme, Gömme Tabanlı Budama, Üretici (LLM) Özetleme, LLMLingua Tarzı İstem Sıkıştırma — her biri tipik bir oran aralığı ve sezgisel bir doğruluk tahminiyle birlikte.
- **Sıkıştırma Etkisi paneli** — kazanılan token sayısı, tahmini korunan doğruluk, kalan boşluk.
- **Maliyet Defteri** — istek başına ve aylık (30 gün) maliyet, sıkıştırma öncesi/sonrası, gün başına istek sayınıza göre ölçeklenir.
- **Gecikme Tahmini** — sıkıştırma öncesi/sonrası yaklaşık ön işlem + üretim süresi.
- **Kural tabanlı öneriler** — sade dilde yönlendirme (ör. "aşım var, sıkıştırmayı artırın", "bütçe sınırda", "sıkıştırma aylık maliyeti ~%X azaltıyor", "doğruluk yüksek riskli görevler için düşük olabilir").
- **İki dilli arayüz** — İngilizce ve Türkçe arasında anında geçiş; tüm etiketler, birimler ve üretilen öneriler, yerel sayı/para birimi biçimlendirmesiyle birlikte anında değişir.

### Nasıl kullanılır

1. `context-ledger.html` dosyasını herhangi bir modern tarayıcıda açın — kurulum, sunucu veya dış API çağrısı gerekmez. Tüm hesaplamalar tarayıcıda, yerel olarak JavaScript ile çalışır.
2. Bir **model** seçin (veya *Özel*'i seçip kendi pencere boyutunuzu, fiyatlandırmanızı ve işlem hızınızı girin).
3. **Sistem istemi**, **konuşma geçmişi**, **alınan/RAG bağlamı** ve **ayrılan çıktı** token sayılarını girin.
4. Bir **sıkıştırma yöntemi** seçin ve **oran** kaydırıcısını ayarlayın (varsayılan olarak o yöntemin tipik değerine gelir).
5. Aylık maliyet etkisini görmek için beklenen **gün başına istek** sayısını girin.
6. **Defter** çubuklarını, **Sıkıştırma Etkisi**, **Maliyet Defteri**, **Gecikme Tahmini** ve **Öneri** panellerini inceleyin — hepsi anlık olarak güncellenir.

### Yöntem notları (üretim planlamasında kullanmadan önce lütfen okuyun)

- **Fiyat, işlem hızı ve doğruluk değerleri**, herhangi bir sağlayıcıdan canlı olarak çekilen veriler değil, **gösterge niteliğinde planlama tahminleridir**. Bunlar *yön gösterici* olması amaçlanan sabit varsayılan değerlerdir ve *Özel* model üzerinden tamamen düzenlenebilir. Kullanmayı planladığınız sağlayıcının güncel fiyatlandırma sayfasını her zaman kontrol edin.
- **Korunan doğruluk**, basit bir sezgisel hesaplamadır (her sıkıştırma yönteminin, oran tipik aralığının üzerine çıktıkça hafifçe azalan bir taban doğruluk değeri vardır). Bu, **ölçülmüş veya kıyaslanmış** bir kalite puanı **değildir** — yüksek riskli veya olgusal kullanım durumları için bir orana geçmeden önce gerçek çıktıları doğrulayın.
- Bu araçta sıkıştırma yalnızca **geçmiş + alınan bağlama** uygulanır (sistem istemi veya ayrılan çıktıya değil), bu da çoğu sıkıştırma hattının gerçekte nasıl konumlandırıldığını yansıtır.
- Gecikme, basitleştirilmiş bir `ön_işlem_süresi + üretim_süresi + sabit_ek_yük` modelidir; göreli önce/sonra karşılaştırması için kullanışlıdır — gerçek yük testinin yerini tutmaz.

### Teknoloji

Tek, bağımsız bir dosyada düz HTML, CSS ve saf JavaScript — derleme adımı yok, bağımlılık yok, çalışma zamanında dış ağ çağrısı yok (yalnızca tipografi için kullanılan Google Fonts hariç). Herhangi bir yerde barındırılabilir, gömülebilir veya bağımsız bir teslimat olarak müşteriye verilebilir.

### Özelleştirme / yeniden markalama

- Model şablonları, sıkıştırma yöntemi tanımları ve tüm metinler `<script>` bloğunun başında yer alır (`MODELS`, `ALGOS`, `ALGO_HINT`, `I18N`) — model eklemek, fiyatlandırmayı değiştirmek, dil eklemek veya metni düzenlemek için bu nesneleri güncelleyin.
- Renk rolleri, hızlı tema değişikliği için `:root` içinde CSS özel özellikleri olarak tanımlanmıştır (`--blue`, `--amber`, `--violet`, `--teal`, `--red` vb.).

### Lisans / kullanım

Bu araç ve kaynak kodu, sipariş veren tarafla mutabık kalınan iç, müşteri veya ticari kullanım için "olduğu gibi" sağlanmıştır. Fiyatlandırma, işlem hızı veya doğruluk varsayımlarının kesinliği konusunda herhangi bir garanti verilmemektedir — bkz. yukarıdaki *Yöntem notları*.
