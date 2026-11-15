name: website-build
description: "How to build the first draft of an Oddle restaurant website, how to redesign one afterwards, and how to make a scoped edit to an existing live site: which template to apply, how to overlay the merchant's palette and pick fonts, how the confirmed review maps to page content, which hero and section variants fit THIS brand and THIS set of photos, where dish photos go, and — for an existing site — which tool matches a font/spacing/photo/variant change without touching anything else. Load this before the first build, before any warm redesign that changes layout, skin or section variants, and before editing an existing site's structure or skin."
version: 1.1.0
tags:
  - website
  - build
  - draft
  - skin
  - variants
  - photos
  - edit
  - redesign
---

# Building or editing the site

The hard rules — `dryRun: false` on every write, never overwrite a design the
merchant chose UNLESS THEY EXPLICITLY ASK FOR A REDESIGN (path 4 below), never
`update_skin` on a skin `check_skin_contrast` rejected, variants only from
`list_section_catalog`, dishes before dish photos, nothing goes live — are in
your instructions and hold whether or not you read this file, on a first build
AND on an edit.
This is the craft on top of them: what to choose (or change), and why one
choice beats another for a particular restaurant.

The failure this skill exists to prevent is the every-site-looks-the-same page:
default hero, default everything else, stock spacing, and a merchant who can
tell it was assembled rather than designed. A close second: an "edit my hero
photo" request that rebuilds the whole homepage because the agent treated
every request as a from-scratch build.

## 1. Pick the path

Four ways in, and they are not interchangeable — know which one you're in
before you touch a tool.

**1. New build — a design was applied** (`apply_design_from_image`). The draft
already has a skin and a starting layout — sections either empty or, if you
passed the mockup's `copy`, already seeded with that text. Your job is to
ENRICH, not rebuild. `get_page` "/" to read what is actually there, then
`set_page_sections` to keep the seeded copy that is right, replace placeholders
with real dishes, story, CTAs and photos, and add or remove a section only
where the brand genuinely needs one. The mockup's bands already chose the
variants — keep them unless the content demands otherwise. Change fonts only
if the merchant asks.

**2. New build — palette fallback.** There is no seeded layout, so build one:

1. `list_templates` → `apply_template` to "/". Choose the layout that fits the
   brand — cuisine, positioning, tone — not the first one returned.
2. `update_skin` with the FULL object: `get_site`, take its `skin`, overlay the
   EXACT swatches from the merchant's palette message. `background`,
   `foreground`, `primary`, `accent` go in VERBATIM — never swap in a preset
   because it looks close.
3. `primaryForeground` / `accentForeground` are not eyeballed. Call
   `check_skin_contrast` with the skin you assembled, apply its `fixes`,
   re-check. If it reports `unfixable`, no text colour rescues that surface —
   change the offending `primary`/`accent` to a slightly deeper shade of the
   same hue rather than shipping unreadable text.
4. Fonts: `list_font_pairings` → choose by tone, pass `fontHeading` /
   `fontBody` VERBATIM. Never invent a font.
5. Keep the template's `muted`, `border` and `radius`.
6. `sectionRhythm` sets vertical density: `airy` for fine-dining and minimal
   brands, `compact` for casual, value-dense ones, omit for the default.

**3. Editing an existing site.** The merchant already has a live/draft site
with real content and wants ONE thing changed — a different hero photo,
tighter spacing, a new font, one section's variant, a copy tweak. This is the
common case once a site exists, and it shares nothing with a from-scratch
build:

- **Use the "Page open in the preview" line in your Context section to know
  WHICH page the merchant means.** Never assume `"/"` — the merchant may be on
  `/about`, `/menu`, or any other page. Every existing-page read or write —
  `get_page`, `set_page_sections`, `apply_section_media`,
  `set_section_variant` — targets that current page, not a hardcoded `"/"`.
- **An "Attached element" section, WHEN PRESENT, names the exact target the
  merchant picked in the live preview — scope reads and writes to it instead
  of asking which section they mean.** It is OFTEN ABSENT: most turns carry no
  attachment at all, and on those you work from the previewed page above and
  whatever the merchant names in chat, same as any other edit.
- **Read only the current state you actually need** — the target page, or the
  site's skin for a skin change — the one-read-per-thing rule in the prompt's
  Tools section still holds.
- **Change ONLY what the merchant asked for.** Preserve every unrelated
  section, its copy, its images, its fonts, and every skin field you were not
  asked to change. `update_skin` takes the full object, so an edit means: read
  the current skin, change the one or two fields asked for, and send the whole
  object back — never re-derive a skin from scratch.
- **`apply_template` replaces the page's layout — it is a redesign, not an
  edit.** Do NOT reach for it to change a font, spacing, a photo, or one
  section's variant. Match the tool to the actual ask instead:

  | The merchant wants | Use |
  |---|---|
  | Different fonts | `update_skin` |
  | More spacious / tighter ("make it feel more spacious") | `sectionRhythm` on `update_skin` |
  | A different photo | `set_page_sections` / `apply_section_media` |
  | One section's variant changed | `set_section_variant` |

- **No cold-start review, no interview, no gap-fill placeholder pass.** Those
  belong to the first build. If a fact is missing for the specific thing being
  edited, ask for that one fact — don't reopen the whole brand interview.
- The `check_skin_contrast` requirement still applies to any skin change,
  including a one-field one: check before `update_skin`, apply `fixes`,
  re-check.

**4. Explicit redesign or template replacement.** When the merchant explicitly
asks to change the template, restyle the whole page, or start the design over,
broader changes ARE allowed — `apply_template`, a new skin, a fresh pass over
section variants. Stay inside the scope they actually asked for: one page if
they named a page, the whole site if they said so. Say back what you're about
to replace before you do it — the merchant should hear that their current
layout or skin is going away, not discover it after the fact.

## 2. Map the answers to content — for a build or a redesign

Paths 1, 2 and 4 converge here: turning what you know about the brand into
page content. (Path 3 — a scoped edit — skips this: you already know exactly
what to change.)

**Content (`set_page_sections`).** The CONFIRMED review — enriched and
corrected — is the source of truth: name, cuisine, one-liner, dishes, story,
order and reserve CTAs. Top it up with any explicit merchant answers. A fact the
merchant gave you outranks a fact you researched.

**Chrome (`update_chrome`).** Brand name, nav, footer and socials, from that
same confirmed review.

**Gap-fill, don't block.** Anything still unknown — opening hours, address — gets
a clear placeholder like `[OPENING HOURS]`, and the turn ends with a short list
of what the merchant should send you. Never stall a build waiting on a detail.

## 3. Choosing (or changing) the hero

Applies whenever you're picking a hero — for a new build, a redesign, or a
path-3 edit that changes just the hero. Match the photo first, then vary by
brand. Read the variant list from `list_section_catalog` — the live
vocabulary, never memory.

The photos you have narrow the field:

| What you have | What it unlocks |
|---|---|
| One strong, hero-grade LANDSCAPE photo | the image-led treatments — put that photo in the hero's `media` |
| Several good photos | also the collage and slideshow treatments |
| A weak or portrait photo | a split treatment |
| No usable photo | the type-led treatments |

Inside whatever the photos allow there is usually MORE THAN ONE candidate. Pick
by brand character — calm and minimal, bold and editorial, or busy and
energetic. Two restaurants with equally good landscape photos should not end up
on the same hero.

## 4. Varying section variants — when you're choosing or changing one

Applies to a build, a redesign, or a path-3 edit that targets one section's
variant. Most families in the catalog carry real alternatives — signatures,
story, gallery, `ctaBanner`, press and the seasonal menu among them. Pick each
one for THIS brand and THIS set of photos:

- A full-bleed story banner needs one strong landscape photo.
- A filmstrip gallery suits a brand with many photos.
- A bento or price-list treatment of the signature dishes suits a menu-led brand.
- A contained CTA panel suits a quieter brand.

On a build or redesign, never leave every family on its catalog default — an
all-defaults page is the every-site-looks-the-same failure. The ONE exception
is `locations`, whose variant is auto-set by outlet count: leave it alone.

## 5. Place the photos

Put the photo URLs you have — researched hero and gallery candidates, or
merchant uploads — on the relevant image and media fields during the
`set_page_sections` pass above (or, for a path-3 edit, on the one field the
merchant asked to change).

Per-dish photos are a separate, later call:

```text
apply_section_media({
  slug: <the site slug you were given>,
  path: <the page path you're working on — "/" for a new build, or the page
    open in the preview for an existing-page edit or redesign>,
  assignments: [{ id: <the signatures section's id>, images: [{ url, dish? }] }],
  dryRun: false,
})
```

- `id` is the section the images belong to. For per-dish photos that is the
  signatures section the dishes were just written into. Its id is on the page you
  have already read — do not guess it.
- `dish` is optional and name-matches one of those dishes.
- **Dishes come first.** A signatures section with no dishes yet has nothing to
  match, so it is skipped as a no-target and the photos land nowhere. Write the
  dishes — `add_signature_dishes` and/or the review's dishes via
  `set_page_sections` — before calling this.
- The default mode (`fill`) never overwrites an image already picked: a gallery
  section appends, every other section only fills an empty slot. So this is
  ADDITIVE on top of the `set_page_sections` pass, never a replacement for it.

## 6. Before you hand back a build or redesign

Applies to paths 1, 2 and 4. (A path-3 edit self-checks against one question
instead: did I change only what was asked, and leave everything else exactly
as it was?)

Check yourself against the four things that make a page look assembled:

1. Is the hero the one this restaurant's photos and character earned, or the
   first plausible one?
2. Is at least one other family off its catalog default?
3. Does every image field that could be filled have a real photo in it?
4. Does anything on the page claim a fact the merchant never confirmed?

Everything you wrote lands on the DRAFT. Say what you built and what you still
need — never imply anything went live.
