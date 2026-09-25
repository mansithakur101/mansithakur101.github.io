# Weekly drops: skins, boxes, event pieces, events

The game reads `catalog.json` from this folder on the website
(`https://mansithakur101.github.io/shop/catalog.json`) at launch and whenever it comes back to the
foreground, at most once an hour. With no app update, the file can add:

- skins and box themes to the shop,
- piece sets: ten pictures that replace the pieces during an event (vehicles, fruit, animals),
- events pinned to a week, instead of the built-in rotation.

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
| `shapes` | | Flat shapes this skin has its own art for: any of `hexagon`, `square`, `triangle`. Works with any `style`. |
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

### Flat-shape art

By default, hexagon, square and triangle levels show plain shapes tinted with the skin's palette. To give
a skin its own art there, list the shapes in `shapes` and add ten PNGs per shape:
`skins/<id>/hexagon/0.png` … `9.png`, and the same for `square/` and `triangle/`.

Use the same 384 px canvas as the round pieces. Stardrop > Shop > Export Skin Templates writes
`Tools/SkinTemplates/shapes/hexagon.png`, `square.png` and `triangle.png`, each at the exact size and position.
Follow the template's outline, because the piece collides as that shape.

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

## Piece sets (for events)

A piece set replaces the pieces themselves for an event's runs: ten pictures, smallest to biggest, for
example a bicycle up to a road train. Pieces collide with an outline traced from each picture, so a bus
really is long. Merging works as usual: two of the same size make the next.

```json
"pieceSets": [
  { "id": "vehicles", "name": "Vehicles", "rev": 1, "upright": true,
    "names": ["Bicycle", "Scooter", "Motorbike", "Car", "SUV", "Van", "Pickup", "Bus", "Truck", "Road train"],
    "palette": ["#9CA3B8", "#E8E4F0", "#E0304E", "#2F6BFF", "#1FC98A", "#8E4BFF", "#FFB627", "#FF7A2F", "#3FD8F0", "#FFE45C"] }
]
```

| field | meaning |
|---|---|
| `id` | Folder name: pictures go in `sets/<id>/0.png` … `9.png`. |
| `names` | Optional. What the game calls each size, in the NEXT box and the merge-chain hint. |
| `palette` | 10 colours for the glow, aim line and so on. Required. |
| `upright` | `true`: pieces never rotate, so vehicles stay on their wheels and stack like a traffic jam. `false`: they tumble. |
| `rev` | Bump it to replace the pictures. |

Art rules:

- 384×384 square PNG, transparent background. Keep the drawing inside the canvas; a small margin is fine.
- **Chunky proportions, at most about 2:1.** The game scales every piece so its outline has the same area as
  the round piece of that size. A long, thin drawing would come out very wide and flat, and anything still
  wider than most of the box is shrunk to fit.
- Only pixels at 50% opacity or more count as solid. The outline is the convex shape around them, so gaps
  such as the space between a bicycle's wheels count as solid. Faint glows and shadows are ignored.
- The drawn size doesn't matter; the game normalises it. What matters is the shape.

Sets download as soon as they are listed, even before their event, so list a set a week or more ahead.

## Events

An entry pins an event to the week that contains `week`. Weeks run Monday 08:00 UTC to Monday 08:00 UTC.
Weeks with no entry keep the built-in rotation.

```json
"events": [
  { "week": "2026-10-12", "name": "Rush Hour", "blurb": "Bicycles up to road trains",
    "colorA": "#FFB627", "colorB": "#2F6BFF", "art": 7, "pieces": "vehicles", "drops": 50, "typical": 400 }
]
```

| field | meaning |
|---|---|
| `week` | Any UTC date in the event's week. |
| `name`, `blurb`, `colorA`, `colorB` | The home screen's event card. |
| `art` | Which size (0–9) is pictured on the card. |
| `seconds` or `drops` | The run ends on a clock or after this many drops. One of them is required. |
| `typical` | Roughly what one run scores. Rewards and the event mission scale from it. Required. |
| `pieces` | Optional piece set id. |
| `shape` | `Circle` (default), `Hexagon`, `Square` or `Triangle`. Ignored when `pieces` is set. |
| rules | Optional, as in the built-in events: `pegs`, `bigPeg`, `gravity`, `bounce`, `friction`, `damping`, `pieceScale`, `cooldown`, `limitFromTop`, `wind`, `secondsPerMerge`, `dealMin`, `dealMax`, `comboMax`, `goldEvery`, `bigOnlyFrom`, `hideNext`. Leave any out (or 0) to keep the normal value. |

**Fairness.** Everyone's event points go on one weekly leaderboard, so every player should play the same event:

- **Schedule events at least a week ahead**, so players have fetched the entry and the pictures before the week starts.
- If a player's device doesn't have the entry, they play that week's built-in event.
- If it has the entry but not the set's pictures, they play the event's rules with their skin's normal pieces.
- App versions from before this feature always play the built-in rotation.

## Rules

- Never remove an id someone might own. If a skin leaves `catalog.json`, owners keep it only on devices
  that already cached it. To stop selling something, set `until` and leave it in the file.
- Malformed entries are skipped and logged as `[catalog]` in the device log. The rest of the file still loads.
- Never change an event after its week has started. Players already on the leaderboard would have played different rules.
- Test before publishing: in the Unity editor, the game reads this local `catalog.json` in place of the website.
  Press Play, open the shop, and use Stardrop > Shop > Refresh Catalog Now after editing. To try an event,
  temporarily set its `week` to today.
  `catalog.example.json` has a sample of each kind.
