Designing an Oddle Shop
You are not designing from scratch. The editor ships 10 finished themes with
colours, fonts and buttons already matched. Your job: pick the right one, set the
brand colour, stop.

You are building an e-commerce shop, not the restaurant's website. This is
the rule the others hang off, and it is the one to fall back on whenever a
judgement call is close. The page has one job: get someone to the menu and
through the Add-to-Cart button. Anything that serves the brand at the expense of
that — a full-bleed hero of the dining room, a colour scheme that looks like
their business card, a story block above the fold because the owner is proud of
it — is the website instinct, and it costs orders. Their website can be a
brochure. This cannot.

These are best practice, not limits. Everything below is what to do when
nobody has said otherwise, and it is what to recommend. When the operator asks
for something specific, give them that — say you are departing from the guidance
and why, then do it. get_shop_context returns every section with its id and
current settings, so any component is reachable through sectionSettingData,
and you can see what you are changing before you change it.

Four rules follow from it.

Start white. White background by default. Go dark only when the shop should feel premium.
Colour goes on the button. Brand colour on Add-to-Cart, never on the page background.
Readability beats decoration. This is a shop people order from, not a portfolio piece.
Cards stay light. Whenever the page background is a solid colour, the product cards stay white or near-white. See Step 3.
Step 1: Read the merchant's branding maturity
Before touching anything, place the merchant in a tier. How consistently does
their brand show up across website, social and storefront? The tier decides how
much you design versus how much already exists.

Tier	Who	What you do	Difficulty
A	Established brands with a real brand book (Jollibee, Ya Kun)	Copy it faithfully. Match their site, use their own photos. Don't reinvent a brand that works.	Easy
B	Medium brands, branded but inconsistent (Overrice, HJH Maimunah)	Reconcile the mess into one clean look. You make the calls they didn't.	Hardest
C	Hawkers and local favourites, logo-only or nothing (Mizzy Corner, Jin Ji Braised Duck)	Keep it clean and simple. Don't try to be their branding agency — a tidy shop is the win.	Easier than B
Tier B is the hard one and the most common. Get it right and the rest is free.

State the tier you picked and why, in one line, before you build.

Step 2: Pick a theme
Pass preset to update_shop_theme (or presetStyle to enable_shop_ordering
on a brand-new shop). These are the only 10 valid values:

Preset	Fits	Look	Fonts
oddle	Oddle default	light + purple	Arsenal / Jost
coffee	Cafés, bakeries, brunch	cream + brown	Roboto Slab / Ubuntu
earth	Healthy, organic, veggie	cream + olive	Lora
marine	Seafood, poke, "fresh & clean"	light + blue	Ubuntu
dark	Fine dining, steakhouse, premium	black + gold	Hind Guntur
contrast	Bar, dessert, trendy, night	purple + pink	Bitter / Ubuntu
modern	Fusion, contemporary	black + teal, outline button	Libre Franklin / Nunito Sans
pastel	Patisserie, soft, cute	near-white + soft blue	Roboto
yellow_fruits	Juice, smoothie, tropical, fun	yellow + orange, outline	Sanchez / Quicksand
minimalist	Minimal, high-end, or when unsure	white + black	Nunito Sans
When in doubt, pick minimalist. A clean white shop with black text never
looks bad; a wrongly-chosen bold theme does.

Never leave a shop on "Custom" — that isn't a theme, it's whatever was hand-set.
Always start from a named preset.

Step 3: Set the brand colour — and usually nothing else
The theme is already balanced. Most shops need exactly one change.

Pass brandColor (hex) to update_shop_theme. It maps to the storefront's
button/accent colour — the Add-to-Cart button, the thing customers act on.

Recolour at most 2 slots. Button first. Headings second, optionally, via
settings.colorHeadingsFont. Leave everything else on the theme's values.
NEVER set settings.colorBackgroundColor to the brand colour. That single
mistake is what creates the "one big colour block" look. If you are tempted,
don't.
If the page background IS a solid colour, the cards must stay light. Set
settings.colorContainerColor to #FFFFFF or a near-white. The cards are the
product tiles — the dish name, the price and the Add-to-Cart button all sit on
them, so they are the part of the page that has to be effortless to read.
Three presets ship dark cards and will do this to you by default:

Preset	Background	Cards
modern	#2A272A	#0D0D11 — near-black
dark	#181A1E	#23262A
contrast	#1D0259	#2A0B6D
Two presets already do it the right way, and are the model to copy: marine
puts near-white cards (#F5F3FA) on a strong blue, and yellow_fruits puts
#FFFDF3 cards on a full yellow. Both read as bold, and both keep the
merchandise effortless. Keep the drama in the background and the products on
white.

Passing brandColor already forces a coherent light palette, so the trap is
specifically setting a dark preset and stopping — that is when the dark
cards come through untouched. If you pick modern, dark or contrast
without a brand colour, set colorContainerColor yourself.

Contrast check. If text on that colour would be hard to read at arm's
length, it fails. Pick a darker or lighter variant.
Passing brandColor silently creates low-contrast text. It is also copied
onto colorSectionHeadingsFont and, when the header background is white, onto
the header's navigationLinkFontColor. With a light or bright brand colour
(yellow, lime, pale pink) that leaves category headings and the nav links
nearly invisible on white — breaking the contrast rule above in the very call
meant to apply the brand. Whenever the brand colour is light, pin them back:

settings: { "colorSectionHeadingsFont": "#1A1A1A", "buttonTextColor": "#1A1A1A" }
sectionSettingData: {"sections": {"header": {"settings": {"navigationLinkFontColor": "#1A1A1A"}}}}
Then LOOK at the preview. Confirmed live on beta 2026-08-07 with #F0A818.

Getting the colour when the merchant hasn't given you one
research_brand_assets reads the logo's DOMINANT pixel, which on most logos
is the BACKGROUND, not the brand. Wheathead's came back #FBF4D6 — the cream
field behind a terracotta wordmark. Put that on the Add-to-Cart button and you
get exactly the "pale invisible button" this guide forbids. Look at the logo
and take the INK. Wheathead's real colour is the #904824 of the lettering,
and the shop reads as theirs the moment you use it. Treat the returned hex as a
candidate to check by eye, never as the answer — the tool's own note says to
confirm it, and this is why.

Call research_brand_assets first. It reads their website (or Instagram, or an
explicit logo URL) and returns a vetted, re-hosted logoUrl plus a colour.

It returns colour: null when it cannot find one, and that is correct
behaviour — not a failure. When it does:

Look at the logo it returned and take the one dominant colour that is not
black, white or grey.
Logo is only black/white? Treat them as minimalist and skip the colour change
entirely.
Nothing at all to go on? Do not invent a rainbow. Either keep the theme's
own colours, or pick one safe colour by cuisine:
warm food → deep red/terracotta · fresh/healthy → forest green ·
seafood/Japanese → deep blue/teal · premium/bar → black + gold ·
dessert/cute → dusty pink.
Always avoid: neon or fluorescent colours, more than one bright colour
competing, pale invisible buttons, low-contrast text.

Fonts and buttons — usually leave them
Each theme already pairs two fonts well. Only override if the brand is strongly
serif ("classic") or sans ("modern") and the default clashes. If you do:
2 fonts max, and never put a decorative face (Pacifico, Abril Fatface, Rye)
on body text. Use settings.typographyHeadings / settings.typographyBodyText.

Pairings that always work: Playfair Display + Lato (elegant) · Bitter + Ubuntu
(warm) · Oswald + Roboto (bold) · Josefin Sans + Cabin (clean) · Merriweather +
Nunito Sans (trustworthy).

Buttons: Solid + Round is safe. Square only for a sharp or luxury brand
(settings.buttonStyle, settings.buttonShape).

Step 3b: FIND the photos. That is your job, not the tool's.
No tool returns photographs. research_brand_assets returns a logo, a colour,
menu links and social handles - and stops. If you skip this step you will build a
text-only shop for a merchant who has excellent photography, which is what happened
on the first Jumbo Seafood build: the conclusion "they have no usable photo" was
false, and the photo was two clicks away.

You have web access. Use it. Look in this order - it is ordered by how strongly
the photo is provably theirs:

Their own ordering site. research_brand_assets hands you menuLinks; follow
them. Many SG merchants already run an Oddle shop there, and its banner is a
ready-made hero at the right ratio - Jumbo's was 1200x500, exactly the slide
spec, already on Oddle's own CDN. This is the best case and the first place to
look.
Their website. Home page, an "our food" page, the menu page.
Their Instagram grid. discoveredSocials.instagram comes back from research.
A restaurant's grid is mostly food, shot by them.
Their delivery-platform listing - Uber Eats, Foodpanda, GrabFood. Dish-level
photos, and the platform did the quality control.
Google Business / Maps photos - last resort, because the good ones are often
customer snapshots rather than the merchant's own.
Then LOOK at it before you use it. Download it and open it. You are judging:
landscape (a portrait crops to a strip), at least 1200x500 for a banner or hero and
2048x1024 for a wide banner, sharp, and food-forward. Blurry, dark, portrait, a
photo of the shopfront sign, or a group of staff - skip it. A clean text shop
beats an ugly picture, and that rule has not changed; what changed is that you must
go and look before concluding there is nothing.

Provenance is yours to enforce, because the tool does not. It checks the link is
reachable and is an image, then copies the file onto Oddle's own hosting - so an
expiring or hotlink-protected URL is fine, you only need it to work once. What it
does NOT check is whether the photo is theirs. Stock food photography on a real
merchant's storefront is a lie about their kitchen, the same line that rules out
AI-generated dishes. Take photos from the sources above and nowhere else. Unsure?
Ask the rep rather than guessing.

Then pass the URL:

ONE good photo -> wideBanner: { imageUrl, headerText }.
Genuinely 2-3 strong ones -> hero: { slides: [...] }.
Never both - the tool refuses it.
Item photos are NOT hand-attached here. They arrive with the menu when the menu
itself is sourced from a delivery platform; a shop whose items have no photos is a
List + images-off shop (Step 4).
Step 4: Match the Products layout to the photos that exist
This is the highest-leverage decision in the whole build and the one most often
got wrong.

A bad photo hurts more than no photo. Food images have one job: make people
hungry. Blurry, dark or badly shot → do not use it. A clean text menu beats an
ugly picture every time.

Situation	Layout	Images
Most items have strong, consistent photos	Grid	ON
Few, blurry, or no photos	List	OFF
Only some items have photos	List + OFF, or move the photographed items to the top and use Grid	—
Switching a no-photo shop to List + images off is the single change that
fixes most ugly shops. Never ship a Grid with images ON when only half the
products have photos — mismatched empty tiles look broken.

One exception: if items run in a real sequence (Bento 1-5), keep the order even
if one lacks a photo.

How to set it. There is no first-class parameter; it lives on the products
section. get_shop_context returns that section with its current settings, so
read the id from there and write through sectionSettingData:

{"sections": {"<products section id>": {"settings": {"productLayout": "list", "showProductImage": false}}}}
The fields are productLayout (grid | list), showProductImage (boolean),
productCategoryLayout (side | top) and productImageAspect (cover).
Call get_shop_context again afterwards to confirm the change landed — it will
show the new values.

Also make the menu readable: clear category names, every item with a name and a
price, short descriptions only where they help.

Step 5: Sections, header to footer
Sections stack top to bottom. Header, Marketing Popup and Footer are fixed.
Everything else is optional. Only add a section if it earns its place.

The order:

Header (fixed)
Announcement banner
Hero or wide banner — one, never both, and only when it EARNS the space it
pushes the menu down by: they take reservations through Oddle (a second real
call to action), or the photography is genuinely strong. Neither? Skip it and
put the brand colour in the announcement banner instead.
Image with Text — an "about us", a signature dish, or an award
Products
Footer (fixed) — contact, opening hours, address, socials
Image-with-Text sits above Products on purpose: it builds trust before people
reach the menu. Keep everything above Products short so the menu still lands in
the first fold, not three scrolls down.

Skip any of 2-4 that the merchant has nothing real for. A shop that goes
Header → Products → Footer is correct when there is no notice, no reservation and
no story — it is not an incomplete shop.

Header (fixed) — logo or shop name, nothing competing. Get the spelling and
capitalisation right; it is the first trust signal. Pass logoUrl to
update_shop_theme. Never stretch a tiny, low-res logo — a clean text name
beats a blurry image. Never ship a placeholder or default header.
Announcement Banner (optional, no photo needed) — the safe way to add brand
colour and personality without images. Pass announcement with a bold
headerText and one short bodyText. One or two messages, never more. Keep it
current — an old "Closed for CNY" in July reads as a neglected shop. Go easy
on emojis. Never leave it empty or generic.
Slideshow / Wide Banner — photo required. 1-3 slides, headline of 3-6
words, clear button ("Order Now"). No good photo? Skip it. An empty or
low-res hero is the number-one cause of an ugly shop; use an Announcement
Banner in the brand colour instead.

NEVER BOTH. A hero slideshow and a wide banner are the same section done
two ways, so a page carrying both shows the customer two competing "Order Now"
buttons before they reach the menu. The tool now refuses a call that asks for
both, and warns when a page ends up with both. Pick one and say which.

With exactly ONE good photo, use the wide banner, not the slideshow. A
one-slide carousel reads as something someone started and abandoned; a wide
banner reads as composed. Save the slideshow for genuinely 2-3 strong photos.

Judging the photo is your job, not the tool's. It checks the link is
reachable and is an image, and copies the file onto Oddle's hosting. It does
NOT check the photo belongs to this merchant. Stock food photography on a
real merchant's storefront is a lie about their kitchen — the same line that
rules out AI-generated dishes. Take photos from their own site, their social
accounts, or their delivery-platform listing, and when you are not sure, ask
the rep rather than guessing. Blurry, dark, portrait-shaped, or a photo of
the shopfront sign are all "skip it" — put the brand colour to work in an
Announcement Banner instead.

You may remove a hero only when the operator asks you to, via
update_shop_theme with remove: { hero: true }. What you must never do is
decide for yourself that one is empty and bin it: a slide carries a headline,
a subheading and a button label as well as an image, and the storefront
decides what to draw from a different field than the one you would naturally
check — so "this banner is empty" is not a judgement you can make safely, and
a wrong call deletes the merchant's own content.

When a shop has a blank-looking hero, SAY SO and ask whether they want it
taken off. If they say yes, remove it, show them the preview, and pass on the
removed content the tool hands back so they can restore it if they change
their mind. Never claim you removed something you did not.

Products — see Step 4.
Image with Text — photo required. Only when there is a real story: history,
an award, a Michelin dish, a famous chef. One photo, 1-2 short sentences, used
once, high up. No photo → skip it.
Reservations — only if the merchant actually takes bookings. A dead button
erodes trust.
Instagram Feed — only if the account is active and on-brand. A stale feed
looks worse than none.
Marketing Popup (fixed) — only for a real, current offer.
Custom HTML — leave it alone unless someone technical provides the code. It
is the easiest way to break a shop's layout.
Footer (fixed) — fill every field: contact, opening hours, address, real
social links. This is where customers confirm you are a real business. Wrong
hours cost real orders. Never leave placeholders or link dead accounts.
WHAT THE TOOLS CAN AND CANNOT REACH (corrected 2026-08-17 — the previous
version of this note was written before the section work and is no longer true).

First-class: header logo · announcement banner · hero slideshow · wide banner ·
image-with-text · footer columns and socials · removing any of those four photo
and text sections · every theme setting via sectionSettingData.

Still NOT reachable, so say plainly that the operator must do it in the
dashboard and never imply you did:

The footer's shipped placeholder column ("Talk about your restaurant").
Every shop has it, it is customer-facing, and filling the footer does not
remove it. Tell the operator to delete that block.
Instagram feed — needs the merchant to connect their own account.
Marketing popup and custom HTML.
The promotions strip — on the newer promotions layout the storefront
draws it and ignores the design section entirely.
The shop's NAME, and the Reservation link in the header nav.
Step 6: Starter looks
Copy these wholesale. Pick the theme (fonts come free), set the brand colour, set
the Products layout. Done.

Look	For	Theme	Colour	Products
Warm Café	Cafés, brunch, bakeries with photos	coffee	brand → button	Grid, image ON
Clean Modern	Modern casual, health food, or unsure	minimalist	brand → button	Grid, image ON
Bold Street Food	Hawker, burgers, fried chicken, bubble tea	contrast	optional → button	Grid, image ON
Elegant Dining	Fine dining, steakhouse, cocktail bars	dark	— (buttons Square)	Grid, image ON, Crop
Fresh & Healthy	Salads, poke, juice bars, seafood	earth or marine	brand → button	Grid, image ON
No-Photo Menu	Any merchant without good photos — the ugly-shop fix	minimalist	brand → button	List, image OFF, + Announcement Banner in brand colour
Craft Bakery	One or two strong brand photos, no per-item photos — very common	coffee	logo's INK → button	List, image OFF, + Announcement Banner + Image-with-Text carrying the good photo
The Craft Bakery row is the shape most small merchants are actually in: a
beautiful shot of the counter or the bake, and nothing per item. Put the good
photo in Image-with-Text where one photo is enough, and keep the menu a clean
list. Do NOT reach for a hero — one photo in a carousel reads as unfinished,
and it pushes the menu below the fold for no gain.

Step 7: The pre-pitch checklist
Run this before showing the shop to anyone. Report each line as pass or fail —
never claim a pass you have not checked.

Do

Clean, right-sized logo and the correct shop name.
Started from a named theme that matches the cuisine.
Brand colour on the Add-to-Cart button.
Kept the theme's fonts.
Footer filled: contact, hours, address, socials.
Every product has a name and a price.
List + image-off when photos are missing.
Previewed on mobile — most customers order on their phone.
Went looking for photos before concluding there are none (Step 3b): their
ordering site, their website, their Instagram, their delivery listing. "No usable
photo" is a finding you earn, not a default.
Opened the preview and LOOKED. Wait for the photos to finish loading first:
the storefront shows a grey blur placeholder while an image loads, and a
screenshot taken too early looks exactly like a broken picture.
Checked the footer for the shipped placeholder column and told the operator
to delete it if it is still there.
Don't

Page background in the brand colour.
Dark product cards on a dark background — the menu has to stay effortless.
Build their website. If a choice serves the brand at the menu's expense, it is the wrong choice.
A banner or Image-with-Text with no real photo.
Concluding a merchant has no photos without checking their ordering site, website,
Instagram and delivery listing. That conclusion has been wrong in practice.
An empty or generic announcement bar.
More than 2 fonts or 2 bright colours.
Grid with images ON when only some products have photos.
A Reservations button when they don't take bookings.
A Marketing Popup with no real offer.
Custom HTML without technical help.
The 10-second gut check: Does it look like a real, cared-for business? Can I
read everything easily? Is the Add-to-Cart obvious? Yes to all three → ship it.

Publishing
update_shop_theme writes a draft. Customers keep seeing the current design.
The response carries a previewUrl.

Give the operator that preview link and let them look before you publish. Then
publish_shop_theme makes it customer-facing, and there is no undo — confirm
explicitly first.

Two things to verify with your own eyes rather than trusting a status code:
a storefront can return HTTP 200 and still render broken, and the menu is
client-rendered so the raw HTML will not contain the dish names. Open the preview
link. Check it on a narrow viewport too.
