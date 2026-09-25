# Weekly shop drops

The game reads `catalog.json` from this folder on the website
(`https://mansithakur101.github.io/shop/catalog.json`) at launch and whenever it comes back to the
foreground, at most once an hour. Anything listed here appears in the Skins and Boxes tabs with no app update.
Players who are offline see what they last downloaded.

To publish a drop, edit `catalog.json`, add any PNGs, and push this folder to the GitHub Pages repo.
Players get it within about an hour. GitHub Pages caches for up to 10 minutes.

## Skins

| field | required | meaning |
|---|---|---|
| `id` | yes | Unique and permanent, lowercase, no spaces. Ownership is saved under it, so **never reuse or rename an id**. |
| `name`, `blurb` | name yes | Shown on the shop card. |
| `style` | yes | `planet`, `gumball`, `neon`, `monster`, `marble`, `donut`, `bubble`, `galaxy` (an existing look in new colours), or `images` (your own art, see below). |
| `palette` | yes | 10 hex colours, smallest size first. They also colour the hexagon, square and triangle levels, so they are needed even for image skins. |
| `price` | | Coins. For an ad skin, this is what Plus members pay instead. |
| `ads` | | Rewarded ads needed to unlock. |
| `level` | | Unlocks by itself after clearing this level. |
| `from` | | UTC date or date-time the skin goes on sale (`2026-10-02` or `2026-10-02T08:00`). Before then it stays hidden. You can list several weeks ahead. |
| `until` | | UTC date-time it stops being sold. Anyone who owns it keeps it. |
| `rev` | images | Bump it to replace a skin's PNGs. Players re-download them. |

A skin shows a **NEW** tag and sits at the top of the shop for 7 days after its `from` date.

### Image skins

Put ten PNGs in `skins/<id>/`, named `0.png` (smallest) to `9.png` (biggest):

- Square, 384×384 recommended (allowed range 64–1024).
- The ball is a circle **256 px across (two thirds of the width), centred**, with a transparent background.
  Glows and rings may use the margin around it.
- The physics circle is the 256 px disc. Keep the solid part of the art inside it, or balls will look like they overlap.

Stardrop > Shop > Export Skin Templates in the Unity editor writes every built-in skin as correctly laid-out
PNGs to `Tools/SkinTemplates/`. Use them as the starting canvas.

A skin with `"style": "images"` appears only after all ten PNGs have downloaded and passed the checks
(valid PNG, square, 64–1024 px). A bad or missing file hides the skin; it never shows up broken.

## Boxes

| field | meaning |
|---|---|
| `id`, `name`, `blurb` | As for skins. |
| `kind` | Background pattern: `Dots`, `Bubbles`, `Stripes`, `Polka`, `Grid`, `Stars`. |
| `fill`, `rim`, `pattern` | Hex colours of the play box, its outline and its pattern. |
| `rimAlpha`, `patternAlpha` | Opacity 0–1 for the outline and the pattern. |
| `skyTop`, `skyMid`, `skyBottom` | The three sky gradient colours behind the box. |
| `price`, `ads`, `stars` | Coins, rewarded ads, or total stars needed to unlock. |
| `from`, `until` | As for skins. |

## Rules

- Never remove an id someone might own. If a skin leaves `catalog.json`, owners keep it only on devices
  that already cached it. To stop selling something, set `until` and leave it in the file.
- Malformed entries are skipped and logged as `[catalog]` in the device log. The rest of the file still loads.
- Test before publishing: in the Unity editor, the game reads this local `catalog.json` in place of the website.
  Press Play, open the shop, and use Stardrop > Shop > Refresh Catalog Now after editing.
  `catalog.example.json` has a sample of each kind.
