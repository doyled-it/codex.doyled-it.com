# CLAUDE.md: Codex Galdr project guide

This file is context for any future Claude session working on the **Codex Galdr**, a
single-file dark-themed website presenting the lyrics of the band **Heilung** with
side-by-side original/English translation, academic sourcing, and per-civilization
visual theming.

Read this first. It encodes the methodology, conventions, and hard-won decisions so
the work stays consistent across sessions.

---

## 0. Conventions (owner's global rules, recorded here so cloud sessions inherit them)

A cloud session will not see the owner's machine-local global config, so follow these from here:
- **No em dashes in any prose.** Not in page content, commit messages, docs (this file
  included), or code comments. Use a comma, colon, period, or parentheses. Hyphens and en
  dashes (numeric ranges) are fine. The whole site was swept em-dash-free on 2026-10-02;
  keep it that way, and confirm no em dashes remain in index.html (a count of 0) after any edit.
- **Commit messages:** Conventional Commits (`feat`, `fix`, `docs`, `style`, `refactor`,
  `chore`, ...). No attribution footers, and no mention of the AI tooling or its vendor.
- **This CLAUDE.md is intentionally checked in** for this project (an agreed exception to
  the global "never commit CLAUDE.md" rule). Do not untrack it.

---

## 1. What this is

- **Deliverable:** one self-contained HTML file, `index.html` (was `heilung-codex.html`
  during development). No backend, no build step, no external JS dependencies. Fonts
  load from Google Fonts; everything else is inline.
- **Title:** "Codex Galdr." A dark Nordic-occult presentation of Heilung's lyrics.
- **Owner intends to publish** at `codex.doyled-it.com` (Cloudflare Pages + Cloudflare
  DNS), non-commercial, no ads.
- **Status at last edit:** COMPLETE, all three studio albums, 27 songs.
  - *Ofnir* (2015): 9/9
  - *Futha* (2019): 9/9
  - *Drif* (2022): 9/9
- **Tap-to-reveal annotations added (2026-10-02):** 136 grounded, sourced glosses across
  all 27 songs (see §5a). The whole site was also swept em-dash-free and given a prose
  review pass the same day (see §10).
- Possible future work: a *Lifa* (live album) section, future releases, or polish.

---

## 2. THE GOLDEN RULE: research before writing

**Never write an entry from memory.** Every song was verified against primary editions
and scholarship via web search BEFORE anything was added to the page. This is the single
most important convention of the project. The owner explicitly values hard research over
confident recall.

Workflow for each new song:
1. Read the pasted lyrics. Identify what's actually there: it is often NOT what the
   title suggests, and often a *collage* of multiple sources in multiple languages.
2. Web-search each distinct element separately (e.g. for "Tenet": the Sator Square, AGLA,
   Gothic numerals, the Old Norse, the Latin invocations: five separate searches).
3. Find the primary source: the manuscript, inscription, edition, or scholarly translation.
   Prefer Wikipedia for orientation, then originals (skaldic.org, sacred-texts, university
   pages, Bandcamp for the band's own notes) for confirmation.
4. Only then write the entry, citing what was found.

### Honesty discipline (critical)
Distinguish three categories and treat each differently:
- **Verified public-domain historical text** (Eddic poems, rune poems, charms, Sator
  square, Suetonius, Enūma Eliš, the Hurrian hymn). → Reproduce in full, original +
  translation, with sources.
- **Modern copyrighted Heilung lyrics** (original German/English songs like
  Schlammschlacht, Keltentrauer, Afhomon, Carpathian Forest). → See §6 copyright. Present
  as transformative translation/commentary, NOT full verbatim dumps.
- **Genuine fragment-collages / untranslatable text** (Asja, Anoana, the damaged Hurrian
  of Nikkal, the rune-name incantation of Buslas Bann). → Gloss only what is solidly
  known, and EXPLICITLY state that a literal line-by-line meaning is not recoverable.
  Never invent a confident translation. Use a `.frag-note` box to flag this.

When in doubt, say less and flag uncertainty. The owner prefers an honest "this can't be
translated" over a plausible fabrication.

---

## 3. File structure & build method

The page is organized as:
- `:root` CSS variables (colors, fonts) → all styling is in one `<style>` block.
- A top `<nav class="index">` with one `.index-album` block per album, each containing
  `.index-links` of `<a>` links to songs.
- Three `<div class="album">` sections (`id="album-ofnir/futha/drif"`), each with a runic
  header, `.album-title`, `.album-year`, `.album-note`, then its song `<section>`s.
- A placeholder `.addmore` div + `<footer>` at the very end.

### How songs were added (build method)
Large sections were generated with a Python script in the working dir (e.g. `marduk.py`
→ `marduk_section.html`), then inserted with a Python snippet that finds the previous
song's `</section>` and the album-closing `  </div>`, inserting before it. Always:
```
python3 -c "import html.parser; html.parser.HTMLParser().feed(open('index.html').read()); print('HTML parse OK')"
```
to validate, and `grep -cE '<section class="song'` to count. Then update the album-note
track count and the nav.

### Anatomy of a song `<section>`
```
<section class="song [theme-X]" id="songid">
  <div class="song-head"><span class="song-rune">⟨glyph⟩</span><h2 class="song-title">…</h2></div>
  <p class="song-meta">…intro…</p>
  [<div class="culture-key">…color legend chips…</div>]   ← for themed/multi-culture songs
  [<div class="frag-note">…honesty caveat…</div>]          ← for fragment/collage/copyright songs
  <div class="sources"><h4>⚜ Sources</h4><ul>…hyperlinked citations…</ul></div>
  <div class="block [refrain] [culture-X]">                ← side-by-side rows
    <div class="col orig"><span class="col-label">…</span><div class="verse|chant">original</div></div>
    <div class="col trans"><span class="col-label">…</span><div class="verse">English</div></div>
  </div>
  [<div class="glossary"><table>…</table></div>]           ← for name-lists / rune-lists
  <div class="notes"><p><strong>¹</strong> …footnotes…</p></div>
</section>
```
- `.block.refrain` = blood-red-tinted ritual/chant blocks, with a `.refrain-tag` label.
- `.chant` vs `.verse` inside a col: `.chant` for incantatory/list text, `.verse` for prose.
- Footnotes use `<sup><a href="#xx1">1</a></sup>` to `<strong>¹</strong>` in `.notes`.
- Any term can be wrapped `<span class="gloss" data-g="KEY" tabindex="0">…</span>` to make
  it tappable; add the matching `KEY` to the `GLOSS` object (see §5a).

---

## 4. Visual design

Dark Nordic-occult base. Key `:root` vars:
- `--frost:#5fb8d6` (primary accent, "Norse" blue), `--frost-bright` (brighter)
- `--gold:#c9a24b`, `--blood:#7a2e2e` (refrain tint), near-black bg
- Fonts: Cinzel (headers/runes), EB Garamond / Spectral (body/verse)
- Rune + grain texture overlays; `body{overflow-x:hidden}` (guards themed-section bleed)

Link convention (IMPORTANT, applies everywhere, including footnotes):
`color:var(--frost); text-decoration:none; border-bottom:1px dotted var(--rune);`
with `:hover` brightening. There are dedicated rules for `.sources a`, `.song-meta a`,
AND `.notes a`. If you add a new link context, give it this same style so links don't
fall back to default browser blue. The `.gloss` trigger is the deliberate exception: a
frost dashed underline plus a `›` marker, going gold when active (it opens a drawer, it is
not a link).

---

## 5. THE CULTURE-THEMING SYSTEM (Drif's cross-civilization concept)

*Ofnir* and *Futha* stay in the native frost-and-gold (they're Norse/Germanic). Theming
is RESERVED for *Drif*, which Heilung deliberately built to reach beyond the North to
"all great ancient civilizations." This restraint is intentional and meaningful, so don't
theme the Norse-family songs just for consistency. (Asja, Anoana, Nesso, Buslas Bann are
Norse/Germanic and correctly stay default-blue.)

Two layers, both via CSS custom props
(`--c-accent / --c-bright / --c-wash / --c-wash-soft / --c-tint / --c-edge / --c-glow`):

### Layer 1: per-BLOCK tint
Add `culture-X` to a `.block` to tint just that block (left edge, label color, refrain
wash), plus a `<span class="culture-sigil">⟨glyph⟩</span>` before the label text.
Use for MULTI-culture songs where blocks come from different civilizations (e.g. Tenet).

### Layer 2: whole-SECTION theme
Add `theme-X` to the `<section class="song">` to wash the ENTIRE entry: title glow, rune,
head rule, sources panel, a faint background tint (negative margins -22px aligned to the
.wrap's 22px padding), and it sets the `--c-*` vars so child blocks inherit the color.
Use for SINGLE-culture Drif songs (Urbani, Keltentrauer, Nikkal, Marduk).

### Nav link theming
`nav-rome / nav-gaelic / nav-babylon / nav-ugarit` color the index links. For a genuinely
MULTI-culture song, use `nav-blend`: gradient border + gradient text. The link must wrap
its text in `<span class="blend-text">`. NOTE: nav-blend's inner fill is SOLID `#0c1421`
(padding-box) so only the border shows the gradient. An earlier translucent fill made it
look "light-themed," which was a bug; keep it solid.

### The palette (accent / sigil / used for)
| Culture   | Accent    | Sigil | Used for                         |
|-----------|-----------|-------|----------------------------------|
| Rome      | `#c0392b` red    | ☉    | Urbani (full), Tenet blocks      |
| Gaelic    | `#3f8f5f` green  | ❦    | Keltentrauer (full)              |
| Norse     | `#5fb8d6` frost  | ᚠ/ᚱ/ᚨ | default; Tenet/Anoana blocks     |
| Gothic    | `#8a7bbf` violet | 𐌲    | Tenet (Gothic numerals block)    |
| Hebrew    | `#d4af37` gold   | א    | Tenet (AGLA block)               |
| Babylon   | `#3b6ea5` lapis  | 𒀭    | Marduk (full)                    |
| Ugarit    | `#c97b3c` terra. | ☾    | Nikkal (full)                    |

Tenet is deliberately a block-by-block MOSAIC (no section theme) because no single
culture dominates it. Single-culture songs get the full section wash. This distinction
is a design decision; keep it.

A `.culture-key` legend (color-swatch chips) goes under the meta of themed/mosaic songs
so readers can decode the colors.

---

## 5a. The annotation system (tap-to-reveal glosses, added 2026-10-02)

Ported from the sister project (fenian / An Chartlann) and restyled to the codex's
frost-and-gold palette. It lets any term in the lyrics or commentary open a drawer with a
grounded, sourced gloss, so depth that would clutter the page stays one tap away.

- **Trigger:** wrap a term `<span class="gloss" data-g="KEY" tabindex="0">text</span>`.
  The key is lowercase, namespaced by song id (`krigsgaldr_eggja`, `marduk_enuma`, ...),
  letters/digits/underscores only.
- **Data:** one `const GLOSS = { ... }` object in a `<script>` near the end of `index.html`
  (not a separate file). Each entry:
  ```js
  KEY: {
    term: "headword",
    lit:  "short literal gloss",          // italic; omit if none
    note: "<p>grounded explanation (HTML)</p>",
    sources: [{label:"…", url:"…"}],       // links in the drawer
    prov: "confidence / provenance caveat" // shown only when a claim needs qualifying
  }
  ```
- **Drawer + handler:** the `<aside class="drawer">` markup and the event-delegated click /
  keyboard (Enter/Space/Esc, backdrop, focus return) handler are inline in the same
  `<script>`. Accessible; keep it so.
- **Golden rule still applies:** only verified facts go in `GLOSS`. Every genuinely
  contested reading (the Eggja stone, *alu*, Arepo, AGLA's dating, the Högstena amulet,
  Sigrdrífa/Brynhildr, Nikkal's domain) carries a `prov` caveat rather than false certainty.
- **Validate after any gloss edit:** every `data-g` must resolve to a `GLOSS` key and vice
  versa, and the script must parse:
  ```
  grep -oE 'data-g="[a-z0-9_]+"' index.html | sort -u    # vs the GLOSS keys
  node --check <(sed -n '/const GLOSS = {/,/^};/p' index.html)   # JS syntax
  ```
  All 128 citation URLs in the glosses were HTTP-checked (2026-10-02) and resolve.

---

## 6. COPYRIGHT POSTURE (researched; matters for publishing)

Findings from legal research (NOT legal advice; an IP attorney consult is recommended
before going fully public):
- Lyrics are protected literary works within the composition copyright (Title 17).
  Reproduction, distribution, public display, AND translation (a derivative work) are all
  controlled rights.
- Genius/AZLyrics/LyricFind ARE licensed (post-2014 NMPA enforcement, intensified
  mid-2020s). "Community-driven" describes how text is *entered*, not the legal basis.
- There is ~no practical fair-use safe harbor for full lyrics. No-monetization does NOT
  cure infringement. Footer disclaimers have no legal force.

**Practical posture adopted for the codex:**
- Public-domain ancient/medieval texts (the large majority): reproduce IN FULL, original
  + translation. Safe.
- Owner's own commentary, etymology, sourcing: fine.
- Modern in-copyright Heilung songs: present as TRANSFORMATIVE full-arc English prose
  rendering/analysis, with only SHORT German anchor-lines per section (a line or two as a
  pointer), NOT a verbatim full-German dump. Link out to a licensed source (Bandcamp /
  Genius) for the complete original. (Precedent set when expanding Keltentrauer to its
  full 12-block narrative arc.)
- If full modern lyrics are ever wanted: email the publisher (Season of Mist / Heilung
  management) for permission for a non-commercial fan archive.

For publishing: owner was advised to consider a `<meta name="robots" content="noindex">`
(stay link-shared, not Google-indexed) given the modern-song wrinkle, set a good `<title>`,
and add a courteous (non-load-bearing) footer disclaimer noting non-commercial fan use,
public-domain ancient texts, and modern lyrics as translation/commentary.

---

## 7. Per-song source notes (what was verified)

**Ofnir:** Krigsgaldr & Hakkerskaldyr = Eggja stone (N KJ101, c.650–700), Grønvik reading;
Krigsgaldr adds Kai Uwe Faust's English "language of the sword" bridge. Schlammschlacht =
original German, Teutoburg 9 AD (Varus/Arminius), closes "Quintili Vare legiones redde"
(Suetonius, Aug. 23). Carpathian Forest = original modern ENGLISH (Bandcamp). In Maidjan =
7 inscriptions via Senekal 2024 (Negau B, Trollhättan, Lindholm, Thorsberg, Vimose comb,
Nebenstedt, Eggja, Sønder Rind). Alfaðirhaiti = Odin heiti (Grímnismál) + Kragehul I spear.
Fylgija Ear / Futhorck = Anglo-Saxon Rune Poem (Dickins 1915). Afhomon = modern German
(Nietzschean), closes runic "Gibu auja" (Seeland-II-C bracteate) + "leuba" love.

**Futha:** Galgaldr = Völuspá st.45/41 + Högstena amulet galdr + Völuspá st.59/64. Norupo =
full Norwegian Rune Poem (Younger Futhark, Dickins 1915) + Bezenye brooch refrain + Noleby
"runo raginakundo." Othan = Hávamál Ljóðatal charms. Traust = First Merseburg Charm +
Grógaldr. Vapnatak = modern West-Central German dialect (Hunsrückisch) prayer; title = ON
"weapon-taking." Svanrand = valkyrie name-þula (was initially MISLABELED "Krigsgaldr" early
in the project, corrected: real Krigsgaldr is in Ofnir). Elivagar = original German
glacial poem + Eddic list of 11 Élivágar ice-rivers (Gylfaginning 4 / Grímnismál 27–28).
Elddansurin = 8 ON fire-heiti (Nafnaþulur-style); forms an ice/fire diptych with Elivagar.
Hamrer Hippyer = Second Merseburg Charm (Hopkins 2019, mimisbrunnr.info) + Odin-names
(Hangatýr+hamingja, Gangleri) + Eggja "Au is urki." Album closes on a healing charm. Fitting, since "Heilung" = healing.

**Drif (cross-civilization):**
- Asja (1) = ON fragment-collage (frost-blue, default).
- Anoana (2) = Migration-era gold bracteates; ONLY "landawariar" = "land-protector" is
  confidently translatable (per Maria Franz). Norse blocks.
- Tenet (3) = Sator Square (Pompeii palindrome) + ON self-declaration + Younger Futhark
  rune-names + Gothic numerals (Wulfila áins–fimf) + Latin (Sol Invictus/Pax/Fortuna) +
  AGLA (Kabbalistic notarikon "Atah Gibor Le-olam Adonai"). MOSAIC theming + nav-blend.
- Urbani (4) = Caesar's soldiers' triumph-songs, Suetonius Divus Iulius §49 & 51
  (Nicomedes/Bithynia jibe; Gauls-in-Senate). theme-rome.
- Keltentrauer (5) = original German Celt-vs-Rome battle elegy (Gaulish counterpart to
  Schlammschlacht). Anchors: carnyx (Celtic war-horn), Vae victis (Brennus, sack of Rome
  c.387 BC), single-combat custom, Cernunnos/stag-people. EXPANDED to full 12-block arc as
  original English prose + short German anchors. theme-gaelic.
- Nesso (6) = Old Saxon worm-charm "Contra vermem"/Pro nessia (c.900–1000); disease driven
  body→bone→flesh→skin→arrow, cast away (Storms, Grimm). Frost-blue.
- Buslas Bann (7) = "Busla's Curse" (Buslubœn) from Bósa saga; climaxes in ristil/þistil/
  mistil runic riddle (Gørlev/Ledberg grave-binding formula). Sung text is Heilung's
  rune-NAME incantation, not the saga verses. Frost-blue.
- Nikkal (8) = Hurrian Hymn to Nikkal "h.6", Ugarit (Ras Shamra), c.1400 BCE, oldest
  notated music. Lyrics badly damaged/partly understood (Thiel 1977 provisional); honestly
  states verbatim meaning not recoverable. theme-ugarit.
- Marduk (9) = the Fifty Names of Marduk from the Enūma Eliš (Dalley, Myths from
  Mesopotamia; tablets VI–VII; Ashurbanipal library 7th c. BCE). After slaying Tiamat the
  gods grant 50 names (50 = Enlil's number → transfer of kingship). Grouped by assonance
  (Asalluḫi, Tutu, Šazu clusters); ends at Nēberu (Jupiter) then Enlil. German refrain
  "Du hast den Dämon getötet" = Heilung's own. theme-babylon.

---

## 8. Tone & conventions when talking to the owner

- The owner adds songs one at a time. They love the project and care about getting the
  scholarship right.
- Always research first, then build, then validate HTML, then `present_files`, then
  summarize what was verified and any honesty caveats.
- Keep the bilingual format consistent. Update the nav, the album-note track count, and
  (when an album finishes) its completion language.
- Be honest about uncertainty; flag fabrication risk; never pad an entry with invented
  meaning.

---

## 9. Publishing (Cloudflare)

DNS is on Cloudflare; owner is comfortable with setup.
1. Rename file to `index.html`.
2. Cloudflare → Workers & Pages → Create → Pages → Upload assets → deploy.
3. Pages project → Custom domains → add `codex.doyled-it.com` (DNS auto-wires; HTTPS auto).
4. To update later: re-upload a new `index.html` as a new deployment.
Optional pre-publish: set `<title>`, add `noindex` meta, add footer disclaimer.

---

## 10. The 2026-10-02 pass (gloss system, em-dash sweep, prose review)

A single multi-part pass after the 27 songs were complete, all on `index.html`:
1. **Em-dash sweep.** Every em dash (510) was replaced, commas for appositives,
   a colon in the title, period where clauses were parallel. Numeric en-dashes kept.
   The no-em-dash rule (see §0) now holds everywhere, content and docs.
2. **Gloss drawer ported** from fenian and restyled to the frost/gold palette (see §5a).
3. **136 glosses authored** across all 27 songs (Ofnir 41, Futha 46, Drif 44, plus a
   5-term demo on Alfaðirhaiti), batched by album, each research-grounded and sourced,
   contested readings flagged with `prov`. Drafted by per-album research subagents; keys
   applied and validated centrally.
4. **Prose review pass** (per-album review subagents). Fixed 31 issues: comma splices the
   em-dash sweep left behind, a few banned AI-tell words (tapestry/woven, resonance), and
   three over-confident claims aligned with the page's own caveats (the Negau helmet's
   "oldest Germanic text", the Cernunnos/carnyx root, Nikkal as a goddess of orchards and
   wife of the moon god, not a moon-goddess). Also an internal date fix on Nikkal.
5. **Citation URLs link-checked:** all 128 resolve (two Suetonius Wikisource paths were
   corrected from `/Life_of_*` to the live `/Augustus` and `/Julius_Caesar`).

Note: the owner's `reviewing-nonfiction` and `writing-human-prose` skills did not load
inside subagents in that session ("Unknown skill"); the reviewers applied the criteria
directly instead. If a future session wants the packaged skills exercised in-process,
check why they are not resolving for subagents.
