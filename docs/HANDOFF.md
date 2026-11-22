# Handoff: open work until Friday

Written 2026-11-23 for Muse and the owner, so the work can continue while Claude is out of quota.
Rules that still hold:
- Never weaken, skip or work around a check; fix the code, not the test.
- Merge only with every check green and a clean review, pinned to the head SHA.
- No keys, secrets or personal data in code or PR text.
- Still on hold: **35, glance, quay-35, slice D**. No bot repositories.

## Merged this session (for reference)
| PR | Merge commit |
|---|---|
| elghaly-dev/thoth#22 (Play pack) | `062f831` |
| elghaly-dev/thoth#24 (luxury look) | `37fe16b` |
| elghaly-dev/thoth#23 (Deal in a Minute + look) | `30d117c` |
| elghaly-dev/chatting#6 (legal pages + redirect stubs) | `bbe7f21` |
| elghaly-dev/chatting#7 (banned-word sweep) | `033f08e` |
| elghaly-dev/plumb35-site#3 (footer address) | `2aff518` |
| elghaly-dev/plumb35-site#4 (Ask the Faithful Servant) | `8154c71` |
| elghaly-dev/plumb35-site#6 (Kamino cards, task E) | `038a638` |
| elghaly-dev/plumb35-site#7 (banned-word sweep) | `6aef677` |
| elghaly-dev/iris-35#33, #34 | `89ba13b`, `eeb39c6` |
| elghaly-dev/elghaly-site#8 | `c5fba58` |

## Open PRs
| PR | Head | CI | What is left |
|---|---|---|---|
| thoth#18 Security hardening | `58ecbb5` | red: personal-data-scan | Waits for the owner to set the `PERSONAL_SCAN_FINGERPRINTS` secret. No other red. Then merge main in and merge. |
| ra#3 Security hardening | `3f8e3b0` | red: personal-data-scan | Same secret. Scanners are now Node (`.github/security/*.mjs`); guard and site-check green. |
| ra#5 Play pack | `662d653` | green | Waits for the Play signing secrets and the assetlinks SHA-256 fingerprint (owner). |
| ra#6 Servant memory | `7b378f0` | green | Owner review. |
| thoth#9 zh-CN UI | `b977d04` | no checks ran | Old; check against main before any merge. |
| thoth#8 Ask Thoth video script | `09ca29b` | green | Docs only; owner decision. |
| chatting#5 License link fix | `8e5c959` | no checks ran | Probably superseded by #6/#7; check and close or rebase. |
| chatting#4 Terms and Privacy | `bfc5e47` | green | Superseded by #6 (legal pages are on main); close. |
| chatting#3 New icon | `ae54fc3` | no checks ran | Owner decision on the icon. |
| elghaly-site#11 Home button labels | `0ded2c0` | green | Owner review. |
| elghaly-site#5 AdSense off + consent | `6561cf5` | no checks ran | Owner decision. |
| elghaly-site#1 License link + delivery time | `e673aed` | no checks ran | Old; check against main. |
| 999#85 Result screen | `8c122fd` | red: prayer-, qr-, lock-browser | Muse's branch. Fix below. |
| 999#84 Loud sound, Servant home | `7d5177e` | red: prayer-, qr-, lock-browser | Same overlay cause as #85. |
| 999#87, #86, #83, #82, #81, #80, #79, #78, #62, #57 | see GitHub | green | Owner review; #62/#57 are the Play wrapper and service worker. |

### 999#84 / #85: the fix for Muse
The first-launch overlay (`#onboardOverlay`) covers the buttons the browser tests click. Start each test as a returning visitor:
- `tests/prayer-browser.test.mjs` line 44:
  `await ctx.addInitScript("try { localStorage.setItem('satar-onboarded', '1'); } catch (e) {}" + NOTIFY + init);`
- `tests/qr-browser.test.mjs` after lines 54 and 313, and `tests/lock-browser.test.mjs` after line 33:
  `await ctx.addInitScript(() => { try { localStorage.setItem('satar-onboarded', '1'); } catch (e) {} });`

This gives prayer 68/68 and lock 58/58. One real failure remains on #85 in qr-browser: the new history stores the full scanned text (`satar-history`), so "nothing stored" fails. The owner or Muse decides: store only verdict, reason and time, or keep QR scans out of history.

Also on #85: visitors who skip the name step are greeted as "Wyndham". The default should be a plain greeting.

## Task E: Kamino cards (done for plumb-35; spec for any other site)
- Signatures only from the live page, copied character for character; check each against the live page before committing.
- One card per transaction: date, "Kamino flash loan · <amount> · borrowed and repaid in the same transaction", short signature (first and last six characters), "Open on Solscan ↗" with the full `https://solscan.io/tx/<signature>` link.
- Under the cards, exactly: "Historical on-chain record. Not an offer or investment."
- No profit, APY, APR, yield or return wording.
- Any card without a signature is removed, and named in the PR. On plumb-35, the "Flash path · 15k USDC" screenshot card had none and was removed.
- Same markup on every language page; run the site's house-rules check.

## Task F: banned-word sweep (done: chatting, plumb35-site; elghaly-site had no real hits)
- Words: safe / safety, free, and "AI" used as a title (title becomes **Faithful Servant**). In other languages:
  - Arabic: «آمن»، «أمان»، «مجاني»
  - Russian: «безопасн…», «бесплатн…», «ИИ»
  - Chinese: 安全, 免费, 人工智能
  - Hindi: सुरक्षित, मुफ़्त
- Scope: visible text, meta and social tags, alt, title, aria-label, placeholder, and the strings in i18n JS files, for en/ar/ru/zh/hi.
- Legal pages (terms, privacy, refund, delivery) are flagged, never rewritten.
- Generic "AI" becomes "assistant"; "safe" becomes "private" or "with care"; "free" becomes "no-cost". Keep the meaning, and flip any yes/no answer the rewording changes.
- One PR per repo; the site's own checker must pass. Where a checker keeps a baseline of allowed hits, remove the entries you fixed; never raise them.
- **Left to do:**
  - **iris-35:** about 90 real hits across EN/AR/RU/ZH and `i18n.js`/`boot.js`. "free" is the core pitch ("free server to think and ship"), so the owner should approve the new wording first.
  - **plumb35-site `quay/index.html`:** "Free" ×3; quay is on hold.
- A Russian word ending in «-ии» is not «ИИ»; match «ИИ» as a whole, upper-case word only.

## Known issues to raise with the owner
- **iris-35 access:** this session could not push to or merge in iris-35 (the Claude GitHub App is not installed there). Fix at claude.ai/connect-github.
- **plumb35-site footer check:** `scripts/check_site_rules.py` expects the footer address as "Elghaly LLC, 548 Market St, San Francisco, CA 94104, USA" (commas), but the merged footer uses "·". Another rule there says "LLC" must not appear in the footer at all. Both are switched off today (`FOOTER_ENFORCED = False`), but they will fail as soon as they are switched on. The owner decides the wording first.
