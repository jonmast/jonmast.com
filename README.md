# jonmast.com

Personal resume splash page. A single static `index.html` — no build step, no
dependencies, no external requests. Deploy by copying the file.

## Local preview

```sh
python3 -m http.server 8000
```

Then open <http://127.0.0.1:8000/>.

## Design history

The layout was chosen from a five-variant UI prototype. The full set — an
editorial minimal, a terminal, a datasheet grid, a light monospace console, and
the split rail that won — lives on the `prototype/splash-variants` branch,
along with the `?variant=` switcher used to compare them.

```sh
git checkout prototype/splash-variants
```

**Verdict:** variant E ("split"). The terminal variant had the most personality
but read as unfriendly for hiring; E keeps the dark, monospace character in the
left identity rail while the experience section a hiring manager actually reads
stays on a light, conventional background.

## Content

Sourced from `resume.odt` (last revised Feb 2023, untracked — it contains a
phone number) and <https://github.com/jonmast>.

Known staleness: the Syatt bullets describe the AWS Lambda pipeline and the
Workarea/VTEX/Shopify work, but nothing from the last ~3 years — likely the
work that earned the Staff title. Worth adding.
