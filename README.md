# price scraping proxies: how to pull accurate competitor prices without getting blocked, plus the full 9Proxy plan math

You build a price scraper, it works on your laptop, then it dies in production. Requests come back as 503s, a CAPTCHA page, or — worse — a valid page with the wrong price on it. The product page loaded fine, the selector matched, the number got stored. It just wasn't the number a real shopper sees.

That last failure mode is the one that costs money. Everything else announces itself. Wrong prices quietly poison the dataset, and you only notice when someone asks why a competitor's price history has a $0.00 dip and a three-day plateau that matches your ISP's subnet behaviour.

Proxies are the layer that fixes this, and "price scraping proxies" is a shopping question with three parts: which IP type, which billing model, and which provider won't wreck the budget. Below is the practical version of all three, with 9Proxy's current plans laid out in full because their pricing structure is genuinely relevant to the cost math.

## Why price scraping falls apart at the IP layer

Retail sites are aggressive about repeated requests from one address, for reasons that have nothing to do with you. Amazon throttles a subnet within minutes when it sees a crawl pattern. Cloudflare-fronted shops challenge anything that fails a fingerprint check. Marketplace pages behave differently again — the same product listing on Amazon serves different prices, currency, shipping promises and Buy Box winners depending on where the request originates.

So the same scraper gets three distinct failure types:

- **Rate limiting per IP** — throttling that escalates to blocks
- **Region-locked pricing** — the right page structure with the wrong regional variant
- **Bot challenges** — JS challenges, CAPTCHAs, or an interstitial that returns HTTP 200

Residential IPs address all three because the exit node looks like an ordinary home connection in a real city, and countries, states and cities are selectable. Datacenter ranges are cheaper and faster but get throttled sooner on exactly the targets price data lives on.

One thing to settle before you buy anything: **prices are location-dependent by design**, so the IP location has to match the market you're tracking. If you're comparing a retailer's prices across Germany, the UK and Spain, a single US residential exit gives you three wrong answers.

## Rotating or sticky? It depends on what you're collecting

This decision affects your cost more than the choice of provider.

**Catalogue crawls want rotation per request.** You're walking category pages, grabbing hundreds of listings once, and moving on. Every request can come from a fresh IP, and there's no state to preserve.

**Listing monitoring wants a session that holds.** If you're tracking one product's price over time — or you need a cart, a login or a location cookie to persist while you page through results — you want the same IP across that sequence, then a clean break.

**Some jobs need both.** Many teams run catalogue discovery on rotation and per-SKU verification on sticky sessions, which is why providers keep both modes available on the same endpoint.

There's a second session consideration that only shows up with residential networks: individual IPs are real consumer connections, so they go offline when someone's router reboots or their ISP rotates the lease. 9Proxy's docs put natural IP duration at a few hours up to roughly 24 hours, and their auto-refresh swaps a dead exit for a live one within about 60 seconds. If your pipeline doesn't handle mid-job swaps, that's a retry-logic problem rather than a proxy problem — but it does mean "one IP per worker, forever" isn't a design that survives contact with residential traffic.

## Do the bandwidth math before you choose a billing model

This is where most budget surprises hide. Per-GB billing is fine until your target renders a JavaScript-heavy product page, and then page weight becomes a line item you don't control.

Rough numbers from a proxy vendor's Amazon writeup: a product page is about **600 KB HTML-only, or around 4 MB with every asset**. That means 100,000 product pages HTML-only is roughly **57 GB**. Multiply by a heavier render — say a JS-rendered page at 2–4 MB — and the same crawl can be 200 GB or more.

Run that against per-GB rates and the pattern is obvious:

| Crawl | Traffic | At $3.00/GB (entry tier) | At $1.00/GB (200 GB tier) |
| --- | --- | --- | --- |
| 100,000 pages, HTML-only | ~57 GB | ~$171 | ~$57 |
| 100,000 pages, full render | ~200 GB | ~$600 | ~$200 |
| 1,000,000 pages, HTML-only | ~570 GB | ~$1,710 | ~$570 |

Two conclusions follow. First, fetch HTML only wherever you can — cutting images and media removes most of the bill. Second, if your volume is steady and your pages are heavy, **per-IP billing with unlimited bandwidth flips the equation**: one IP at a fixed price can pull 100 pages or 10,000 pages for the same money, and page weight stops mattering.

That's the fork in the road, so let's look at a provider whose pricing actually sits on both sides of it.

## What 9Proxy is, and where it fits

9Proxy is a residential proxy network with 20M+ IPs across 90+ countries, HTTP/HTTPS and SOCKS5 support, and targeting down to country, state, city, ZIP and ISP level. It's aimed at scraping, SERP monitoring, ad verification, market research and price intelligence — the provider's own listed audience includes price aggregation and e-commerce data teams.

Two usage models, and they behave differently enough that the choice is worth understanding before you look at prices:

**Residential by IP** — you buy a fixed number of IPs, bandwidth is unlimited while an IP is active, and unused IPs never expire. You authenticate through a desktop client that does local port forwarding, with optional proxy authentication. This is the model for long sessions, heavy pages and identity-dependent tasks.

**Residential by GB** — you buy traffic, generate unlimited endpoints, and rotate or hold sessions as you like. Authentication is username/password or IP whitelist, and everything runs from the dashboard without installing anything. Traffic on GB plans is valid for 180 days (unlimited on Enterprise).

That app requirement is the sharpest practical difference between the two. If you're running a scraped job on a Linux server or inside a container, the GB model is the one that works without a Windows box in the loop.

## 9Proxy's full current plan list

9Proxy raised prices on IP-based and bundle packages on 1 June 2026 — the first adjustment since launch, per their own announcement. GB-based pricing was left unchanged. The figures below reflect the post-adjustment structure.

| Category | Package | Price | Effective rate | Notes | Get it |
| --- | --- | --- | --- | --- | --- |
| By IP | 100 IPs | $24 | $0.24/IP | Unlimited bandwidth per IP | [ Start with 100 IPs](https://bit.ly/9-Proxy) |
| By IP | 500 IPs | $72 | $0.144/IP | Small-scale monitoring | [ Compare the 500 IP package](https://bit.ly/9-Proxy) |
| By IP | 1,000 IPs + 500 bonus | $126 | $0.084/IP | Bonus IPs on the 1,000 tier | [ See the 1,000 IP tier](https://bit.ly/9-Proxy) |
| By IP | 2,500 IPs | $210 | $0.084/IP | Multi-vertical workloads | [ Check the 2,500 IP tier](https://bit.ly/9-Proxy) |
| By IP | 5,000 IPs | $360 | $0.072/IP | Agency-scale catalogues | [ View the 5,000 IP tier](https://bit.ly/9-Proxy) |
| By IP | 15,000 IPs | $720 | $0.048/IP | Regional teams | [ Get the 15,000 IP tier](https://bit.ly/9-Proxy) |
| By IP | 25,000 IPs | $863 | $0.035/IP | Heavy automation | [ See the 25,000 IP tier](https://bit.ly/9-Proxy) |
| By IP | 50,000 IPs | $1,438 | $0.029/IP | Reseller level | [ Check the 50,000 IP tier](https://bit.ly/9-Proxy) |
| Business IP | 100,000 IPs | $2,300 | $0.023/IP | High-volume tier | [ Ask about 100,000 IPs](https://bit.ly/9-Proxy) |
| Business IP | 200,000 IPs | $4,140 | $0.021/IP | Platform-scale | [ Compare 200,000 IPs](https://bit.ly/9-Proxy) |
| Business IP | 500,000 IPs | $8,625 | $0.018/IP | Lowest per-IP rate published | [ See the largest IP package](https://bit.ly/9-Proxy) |
| By GB | 5 GB | $15 | $3.00/GB | Bandwidth only, 180-day validity | [ Try a 5 GB pack](https://bit.ly/9-Proxy) |
| By GB | 50 GB + 5 GB bonus | $105 | $2.10/GB | Light ongoing scraping | [ Grab the 50 GB + 5 GB pack](https://bit.ly/9-Proxy) |
| By GB | 100 GB | $150 | $1.50/GB | Regular automation | [ Compare the 100 GB pack](https://bit.ly/9-Proxy) |
| By GB | 200 GB | $200 | $1.00/GB | Medium monitoring jobs | [ See the 200 GB pack](https://bit.ly/9-Proxy) |
| By GB | 1,000 GB | $800 | $0.80/GB | Daily price monitoring at scale | [ View the 1,000 GB pack](https://bit.ly/9-Proxy) |
| By GB | 2,000 GB | $1,500 | $0.75/GB | Multi-region pipelines | [ Check the 2,000 GB pack](https://bit.ly/9-Proxy) |
| Bundle | 100 IPs + 5 GB | $30 | — | IPs plus flexible traffic | [ Try the starter bundle](https://bit.ly/9-Proxy) |
| Bundle | 1,500 IPs + 50 GB | $180 | — | Mixed workloads, client projects | [ See the mid bundle](https://bit.ly/9-Proxy) |
| Bundle | 5,000 IPs + 500 GB | $720 | — | All-in package for larger shops | [ Compare the largest bundle](https://bit.ly/9-Proxy) |

Bundled traffic keeps the same 180-day validity as standalone GB packs. Above the published GB tiers sits an Enterprise GB tier with unlimited data validity, team seats (one owner plus up to five members), shared non-expiring bandwidth and per-member traffic controls — pricing is quote-based rather than listed. A 2026 Geekflare review notes the top published GB rate reaching $0.68/GB at the 10,000 GB level, which is the cheapest bandwidth figure the network advertises.

The IP column is where the per-unit cost falls hardest: $0.24 per IP at the entry tier versus $0.018 at 500,000 IPs. That's a 92% difference in rate for the same product.

One footnote worth knowing before you plan purchases around promotions: 9Proxy's trials are handled through support rather than a self-serve button, and are described as limited and availability-dependent. An independent review from September 2025 made the same point — you request a trial code from support rather than clicking a free-tier link.

## Matching a plan to a price-scraping workload

Here's how the options line up against realistic jobs rather than abstract "use cases."

**Tracking a few hundred SKUs on a couple of retail sites.** Pages are light, requests are frequent, bandwidth is modest. The 200 GB pack at $200, or the 100 GB pack at $150 if you're under that, covers it comfortably — and since traffic doesn't expire for 180 days, unused balance carries into the next quarter.

**Monitoring heavy or JS-rendered product pages.** This is where per-GB billing becomes a gamble you don't control, because you can't stop a retailer from adding video, carousels and lazy-loaded variants. Take an IP plan instead. 500 IPs at $72 or the 1,000 IP package at $126 with 500 bonus IPs gives you unlimited bandwidth per IP, and page weight stops being a variable. For most solo operators and small teams running one or two e-commerce monitoring stacks, 100–500 IPs is the realistic range.

**Discovery plus verification.** Crawl the catalogue on rotating IPs, then verify prices on sticky sessions from the same city. A bundle covers both without you buying two products separately — the 100 IPs + 5 GB bundle at $30 is a reasonable way to test that pattern before committing.

**Agency work with several clients and regions.** The 1,500 IPs + 50 GB bundle at $180 is better value than buying 1,500 IPs and 50 GB separately, and the 5,000 IPs + 500 GB bundle at $720 follows the same logic at the top end. Both spread their traffic over 180 days, which suits project-based usage where a client's data pull happens in bursts rather than steadily.

**Industrial scale.** Past 50,000 IPs the business tiers take over: 100,000 IPs at $2,300, 200,000 at $4,140, 500,000 at $8,625. At that spend it's worth talking to sales about Enterprise terms anyway, since the unlimited-validity and team controls only exist there.

## Getting a price pipeline running

The signup path is short: create an account, buy the package type that matches your workload, then pick how you connect.

- **Dashboard + Proxy2Web** for GB plans — credential-based proxies you can paste into a browser or script with no install
- **The desktop client** for IP plans — Windows, routing at the OS level so tools without native proxy settings still work
- **The public API** for automated pipelines — session control and usage stats from your own code
- **SOCKS5 support** for anti-detect browsers, proxychains and custom Python or Node scripts

Endpoints export as .txt or .csv, and the dashboard includes ready-made code samples. Targeting is set through the generator — country, state, city, ZIP or ISP — so a Los Angeles AT&T exit for a US grocery price check is a few dropdowns rather than a support ticket.

Two operational habits worth adopting: pull HTML only (images and media are the bulk of the bill), and log the exit IP alongside every captured price. When a dataset looks wrong later, the IP record tells you whether you hit a wrong-region variant or a genuine price change.

## Where 9Proxy is not the right answer

Worth saying plainly, because a plan table alone implies universal fit.

The IP-based model needs the desktop client, which is friction if your scrapers run on headless Linux servers or in containers. GB plans don't have that problem, so if you want IP-per-worker pricing and cloud-only infrastructure, that's the combination to check first. Reviewers have consistently flagged the app dependency as the main usability trade-off.

Individual residential IPs come and go on their own schedule — hours to about a day — so a pipeline that assumes permanent stickiness needs retry logic. The 60-second replacement policy softens it; it doesn't remove it.

The pool is 20M+ IPs. Some competitors advertise networks several times that size, which occasionally matters when you need unusual city-level targeting in a small market. Datacenter proxies, which are the cheaper option for unprotected sites, aren't available yet on 9Proxy — one 2026 directory listing still shows them as coming soon. And for Amazon specifically, expect residential exits plus sane concurrency to be the floor, not a guarantee.

Finally, keep the legal side boring: respect robots.txt and target-site terms, collect public data rather than personal data, and rate-limit your own crawlers so you're not the reason a shop's checkout goes down. Price data is public information; how you collect it is your call, and it should be a deliberate one.

## The short version

Price scraping proxies aren't interchangeable units, and the two mistakes that hurt most are picking per-GB billing for heavy pages and picking rotation when the job needed session continuity. Work out your page weight and request volume first, then choose the billing model, then the provider.

If your workload is bandwidth-light and rotation-heavy, 9Proxy's GB tiers at $1.00–$3.00/GB with 180-day validity are the straightforward fit, and the 5 GB pack at $15 is enough to measure your real per-page traffic before you commit to anything. If your pages are heavy or your sessions need to hold, an IP plan with unlimited bandwidth does more for your budget than any per-gigabyte discount will.

[👉 Compare the current 9Proxy residential plans](https://bit.ly/9-Proxy) before the next pricing revision lands — the last one arrived after more than three years of flat rates, which suggests the current structure will hold for a while, but 100 IPs at $24 and 500 at $72 are the numbers to plan against today.
