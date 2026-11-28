---
title: "A Five-Step Path to a Low-Cost Website: Self-Hosted Server, Hugo, GitHub Pages, Cloudflare Pages, and Baidu Indexing"
date: 2026-11-29T00:00:00+08:00
description: "A retrospective of one website's five-step evolution from a self-hosted server to free CDN hosting: the trigger, cost structure, and leftover problems behind each migration, plus three reusable judgments — static-first content keeps migration cost near zero, operational variables get outsourced layer by layer, and search engine indexing is a pipeline parallel to hosting."
categories:
  - Engineering Practice
tags:
  - Web Hosting
  - Static Site
  - Hugo
  - GitHub Pages
  - Cloudflare Pages
  - SEO
toc: true
---

> This is a retrospective of a website's full evolution from launch to stability, covering five steps: a self-hosted server, a static site generator, GitHub Pages, Cloudflare Pages, and Baidu indexing. Site identity and domain information have been removed; what is discussed is the trigger, cost, and trade-offs behind each migration. The conclusions apply directly to startups and individuals.

The conclusion first: **a stable website does not need a server of your own**. The only hard cost across the whole path is the annual domain fee; hosting and CDN can run on free tiers, and indexing is completed through proactive submission on the webmaster platform. Each migration in the five stages was triggered by one concrete problem: the server brought self-managed operations, hand-written pages brought maintenance cost, GitHub Pages brought mainland-China accessibility and commercial-use constraints, and Cloudflare Pages plus indexing submission closed the loop. By the end of the path, the fixed cost of the website reduces to the domain alone.

## I. Five Stages, One Trigger Each

### 1. Self-hosted server with static pages

The starting point is the most intuitive: rent a cloud server, install a web server, and mount hand-written HTML pages. This stage grants full control, and its cost is equally direct: monthly hosting bills, OS patches, certificate renewal, hardening, and backups — every one of them recurring. For startups and individuals, none of this work relates to the business, and none of it shrinks just because the site is simple — a server hosting a few static pages carries essentially the same operations checklist as one running production workloads.

### 2. Switching to Hugo: separating content from presentation

As the page count grew, the maintenance cost of hand-written HTML surfaced: one navigation change touched every page, and styling had no single source of truth. This step introduced <u>a static site generator (SSG) — Hugo — where content is written in Markdown, layout is controlled by templates, and the build output is still purely static files</u><sup>[1]</sup>.

Worth emphasizing: this step did not change the hosting model — the site still ran on that server. What changed was how content was organized: content settled into a Markdown repository, and pages were produced by a local build. At the time it looked like nothing more than page-maintenance relief; in hindsight it mattered far more, because the build output is just a directory that can be dropped onto any host. Every one of the three migrations that followed took half a day precisely because of this step.

### 3. GitHub Pages: outsourcing the operations

After step two, the only thing left on the server was "static files plus a web server," which no longer justified renting a machine. The build output went into a repository, GitHub Pages took over hosting, and the server was retired.

GitHub Pages is free for public repositories, and the official limits are published plainly: <u>published sites may be no larger than 1 GB, with a soft bandwidth limit of 100 GB per month and a soft limit of 10 builds per hour</u><sup>[2]</sup>; the <u>terms of service also state that it may not be used as free web hosting for online business, e-commerce, or commercial SaaS</u><sup>[2]</sup>. For a content site, none of these limits is reachable in the short term. The real constraint was on the access side: in testing, mainland-China access to GitHub Pages-hosted sites proved unreliable, sometimes reachable and sometimes not — and the site's target users are in mainland China. This was not an optimization problem; it was a hard constraint, so the move was mandatory.

### 4. Cloudflare Pages: the final resting point

Migrating to Cloudflare Pages' free tier resolved three constraints at once. On quotas, <u>all plans include unlimited requests and bandwidth, with the free tier offering 500 builds per month and up to 100 custom domains per project</u><sup>[3]</sup> — for a site at this traffic scale, the free quota is in practice inexhaustible. On the network, <u>deployments are automatically distributed to the provider's global edge network spanning hundreds of cities</u><sup>[4]</sup>. On workflow, once bound to a Git repository, every push deploys; attaching a custom domain provisions SSL, and no server is touched at any point.

Testing showed the site reachable from mainland China with some speed fluctuation at peak hours, but the stability problem was resolved. At this point the infrastructure reached a steady state: the domain points to CDN hosting, content lives in a Markdown repository, and publishing is one build command. There was no further reason to migrate — but step four's leftover problem was not in hosting; it was on the search-engine side.

### 5. Baidu indexing: published does not mean discoverable

Hosting solves "can be accessed," not "can be found." A site on overseas hosting has no internal links or external authority, so crawlers may take a long time to discover it — while for a mainland-China business, being indexed by Baidu is a hard requirement. This step connected the site to Baidu's webmaster platform: <u>after account registration and site verification, resources are submitted through the regular indexing tool, which offers API push, sitemap submission, and manual submission</u><sup>[5]</sup> — API push being the fastest, sitemap best for periodic whole-site submission. Indexing setup belongs on launch day; delaying it only postpones organic traffic.

## II. Cost Structure: What This Path Actually Cost

| Stage | Cost | Recurring effort | Leftover problem |
|---|---|---|---|
| Self-hosted static pages | Monthly cloud hosting bill | OS patches, certificate renewal, backups | All operations on your own hands |
| Hugo static-first | 0 (local builds) | Content and template maintenance | Hosting model unchanged, server bill remains |
| GitHub Pages | 0 | Push to deploy | Unreliable mainland access, commercial-use restrictions |
| Cloudflare Pages free tier | 0 | Same, plus custom domain | Mainland speed fluctuates at peak hours |
| Baidu indexing | 0 | One-time verification, ongoing submission as needed | Indexing results depend on content quality |

The only non-zero cost in the table is the annual domain fee. The more notable observation is the direction of the recurring-effort column: it steps down from "system-level operations" to "content maintenance" — every one of the first four steps removes line items, and none adds any. The genuinely valuable assets on this path were not bought with money: they are the domain and the Markdown repository — the former is the entry point, the latter is all of the content. The infrastructure itself should be driven as close to zero as possible.

## III. Three Reusable Judgments Behind the Path

### 1. Static-first content is what makes migration cost approach zero

The step-three and step-four migrations each took half a day, because what moved was a single build-output directory: change one DNS record, redeploy once on the new host. If a site depends on a database, server-side rendering, or session state, every migration is a data-migration project. Once content (Markdown), presentation (templates), and hosting (CDN) are decoupled into three layers, migration degrades from engineering work to configuration. This is the highest-leverage step in the whole path, and the true significance of step two.

### 2. The migrations share one direction: handing off variables layer by layer

Looking back at the five stages, the direction is remarkably consistent: on a self-hosted server, the OS, certificates, backups, and availability are all your responsibility; after adopting an SSG, builds are automated but the machine is still yours; on GitHub Pages, both machine and build are gone; on Cloudflare Pages, even global distribution and certificates are managed for you. Every step shrinks the surface you are responsible for. For startups and individuals the economics are direct: every operational variable removed converts into time invested in the business. Conversely, any proposal that requires you to manage one more layer yourself should first be asked whether it is worth it.

### 3. Indexing is a pipeline parallel to hosting

Steps one through four solve access; step five solves being discovered, and neither replaces the other. The common mistake is treating "site is live" as the finish line — however stable the hosting, if search engines do not know the site exists, organic traffic is zero. In the mainland-China context, the concrete actions on this second pipeline are fixed: verify the site on the webmaster platform, submit the sitemap, push new content via API. Half a day of work; after that, the only remaining variable is content quality.

## IV. The Takeaway for Startups and Individuals

Compressing the whole path into a decision checklist, executed in order:

1) **Domain first.** The annual fee is the only hard cost on the path; registering early fixes the brand entry point, and every later migration only touches DNS;
2) **Make content static.** Pick a mainstream static site generator, settle content into a Markdown repository, and let the build produce a directory;
3) **Host on a free CDN tier.** A marketing site will not approach free-tier quota limits; at this scale, buying a server or paid hosting is unjustified;
4) **Submit indexing on launch day.** Site verification, sitemap, and API push configured together — do not wait for crawlers to find you;
5) **The repository is the source code.** Content and configuration live in version control; if any host fails, switching costs half a day.

The boundary also needs stating: this path covers static marketing sites only — company introduction, product pages, a blog. Business systems that need login, databases, or payments are out of scope; lightweight dynamic needs (form collection, comments) can be patched with platform function capabilities, but that is a different path. The test is a single question: is the page content fully determined at build time? If yes, this path applies; if no, look elsewhere first.

## Closing

After five steps, the website's final form is: a domain billed once a year, zero-cost hosting, content in a Markdown repository, publishing reduced to one build command, and indexing maintained through proactive submission. No step on this path took more than half a day or introduced any monthly fee, and the outcome is stable access and searchability.

For startups and individuals, the transferable lessons reduce to two sentences: a website's value settles into the domain and the content, so the infrastructure should be driven toward zero; and the return of each migration should be measured by how many operational variables it removes from your responsibility, not by how many features it adds.

## References

1. Hugo official site (static site generator: Markdown content, template-driven layout, purely static build output). https://gohugo.io/
2. GitHub Docs · GitHub Pages limits (1 GB published site, 100 GB/month soft bandwidth, 10 builds per hour, commercial hosting restrictions). https://docs.github.com/en/pages/getting-started-with-github-pages/github-pages-limits
3. Cloudflare · Pages product page (unlimited requests and bandwidth on all plans; free tier: 500 builds per month, 100 custom domains). https://www.cloudflare.com/developer-platform/products/pages/
4. Cloudflare Blog · Build your next big idea on Pages (deployments distributed automatically across the global edge network). https://blog.cloudflare.com/big-ideas-on-pages/
5. Baidu Search Resource Platform · Usage guide (site verification and the regular indexing tool: API push, sitemap submission, manual submission). https://ziyuan.baidu.com/college/articleinfo?id=3329
