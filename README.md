# Atlas Tree Planner — Path of Exile 3.30

An interactive, offline-capable viewer for the Path of Exile 1 Atlas passive
tree, plus a documented 150-point build.

![version](https://img.shields.io/badge/atlas-3.30.0-e3bf63)

**Live:** https://trigun0x2.github.io/atlast-tree-3.30/

## Running it

The viewer is a single static page with no build step and no dependencies.
Because it fetches `atlas.json`, it needs to be served over HTTP rather than
opened as a `file://` URL:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Deployment

**Pages has to be switched on by hand once.** A workflow cannot do it for you:
creating the Pages site needs admin rights that the Actions `GITHUB_TOKEN`
does not carry, so `configure-pages` with `enablement: true` fails with
`Resource not accessible by integration`. Pick either route below — both need
exactly one visit to **Settings → Pages**.

**Deploy from a branch (simplest).** Set **Source: Deploy from a branch →
`main` / (root)**. The site is plain static files at the repo root, so GitHub
serves it directly — no Actions run, nothing to maintain. If you take this
route, `.github/workflows/pages.yml` is redundant and can be deleted.

**Deploy with Actions.** Set **Source: GitHub Actions**, then re-run the
workflow. `.github/workflows/pages.yml` uploads the repo root on every push to
`main`. Worth keeping only if a build step is added later.

`.nojekyll` is present either way, so the files are published verbatim rather
than run through Jekyll.

## Using it

| Action | Result |
| --- | --- |
| Hover a node | Tooltip with its name, region, stats and point cost |
| Click an unallocated node | Allocates it via the **cheapest path** from your tree |
| Click an allocated node | Refunds it, dropping anything it orphaned |
| Scroll / pinch | Zoom |
| Drag | Pan |
| Double-click | Zoom in on that point |
| **Copy share link** | Copies a URL carrying your current allocation |
| <kbd>f</kbd> | Focus the search box |
| <kbd>Esc</kbd> | Clear search and highlights |

The left panel searches nodes by name *or* by effect text and lists all 38
regions with allocated/total counts. The right panel shows points spent per
region, every notable and keystone taken, and the **combined totals** of all
allocated stats — numeric lines with the same wording are summed.

A 150-point budget is enforced: a click that would exceed it is refused and the
counter flashes.

The tree **starts empty** — only the start node is allocated, so you can plan
from scratch. **Load 150-point build** fills in the build documented below, and
**Clear** empties it again.

## Sharing a build

**Copy share link** puts a URL on your clipboard that carries the whole
allocation, so anyone opening it sees your exact tree. The address bar also
stays in step as you click, so you can just copy it from there.

Nothing is uploaded and no account or server is involved — the build travels
inside the link's `#b=` fragment, which never leaves the browser. A full
150-point build is about 150 characters, short enough to paste into chat
without being shortened or wrapped.

The encoding maps each allocatable node to an index, sorts them, and writes the
gaps as base64url varints at 5 bits per character, so adjacent picks cost one
character each. A corrupted or truncated link is detected rather than silently
loading a wrong tree: the viewer says so and opens empty instead.

## Data

`atlas.json` (263 KB) holds the whole tree:

| Key | Contents |
| --- | --- |
| `nodes` | 1,116 nodes: position, type, region, stats, connections, sprite rect |
| `arcs` | Curved connection geometry (35 arcs) |
| `groups` / `groupBg` / `orbitRadii` | Group centres, backdrops, orbit radii |
| `frames` / `sheets` / `startSprite` | Sprite atlas rects for node frames and icons |
| `build150` | The build below, as node ids |
| `bounds`, `start`, `totalPoints`, `version` | Viewport bounds, start node, 150, `3.30.0` |

Node types are `normal`, `notable`, `keystone`, `wormhole` (gateway),
`mastery` (region marker, not allocatable) and `start`.

Sprites in `assets/` are cut at zoom 0.5, so the renderer draws them at 2×.

## The 150-point build

Verified connected from the start node (29045) with no orphans and no
unallocatable nodes: **30 notables + 1 keystone + 119 passives**.

The spend concentrates on content that scales with investment — map quantity
and modifier stacking first, then the three most point-efficient mechanics
(strongboxes, essences, shrines) — and spends only single connector points on
distant mechanics it merely passes through.

### Points by region

| Region | Points | Region | Points |
| --- | --- | --- | --- |
| Maps | 31 | Legion | 2 |
| Strongboxes | 27 | Mirage | 2 |
| Essence | 25 | Viridian Wildwood | 2 |
| Shrines | 15 | Rogue Exiles | 1 |
| Rare Monsters | 11 | Beyond | 1 |
| Magic Monsters | 7 | Settlers of Kalguur | 1 |
| Blight | 5 | Expedition | 1 |
| Abyss | 4 | Breach | 1 |
| Threads | 3 | Betrayal | 1 |
| Ultimatum | 3 | Bestiary | 1 |
| Torment | 3 | Sacred Flora | 1 |
| | | Gold | 1 |
| | | Atlas Anomalies | 1 |

### Keystone

- **Overloaded Circuits** (Blight)

### Notables

**Maps (7)** — Shaping the World, Shaping the Mountains, Shaping the Skies,
Shaping the Valleys, Mounting Modifiers, Malignant Modifiers, Invasive
Adversaries

**Strongboxes (7)** — Augured Cache, Fortune Seeker, Hidden Horrors, Secret
Compartments, Security Measures, Tamper-Proof, Twice Tempted

**Essence (7)** — Amplified Energies, Crystal Lattice, Ill Feeling, Prolific
Essence, Repressed Emotion, Supernatural Genesis, Unbecoming Emotion

**Shrines (4)** — Drawn to Power, Path of Worship, Supplication, Syncretism

**Rare Monsters (3)** — Anomie, Imperfect Reflections, Seeing Double

**Magic Monsters (2)** — Deep Bloodlines, Group Solidarity

It is not allocated by default. Load it with **Load 150-point build** in the
left panel; **Copy ids** exports the current allocation as a comma-separated id
list.
