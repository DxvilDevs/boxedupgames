# Boxed Up Games — GitHub Pages Setup

This site is a **single self-contained HTML file**. The logo, the Kart for
Brainrots thumbnail, and all fonts are embedded directly in `index.html` —
there is nothing else to upload, no build step, and no dependencies to
install. Live stats are fetched from Roblox in the visitor's own browser
when they load the page.

## 1. Create the repository

1. Go to [github.com/new](https://github.com/new).
2. Name it anything — e.g. `boxed-up-games`.
3. Set it to **Public** (simplest option — private repos can also publish a
   public Pages site, but public is one less setting to worry about).
4. Click **Create repository**.

## 2. Add the files

Upload these two files to the root of the repo (drag-and-drop on the
GitHub web UI works fine, or use git — see below):

- `index.html` — the site itself
- `.nojekyll` — an empty file that tells GitHub Pages not to run its
  Jekyll build step on this repo. Not always required, but it avoids a
  category of build-related surprises, so it's included here.

**Using the GitHub web UI:**
Repo page → **Add file → Upload files** → drag both files in → **Commit
changes**.

**Using git from your computer:**
```bash
git clone https://github.com/YOUR-USERNAME/boxed-up-games.git
cd boxed-up-games
# copy index.html and .nojekyll into this folder
git add .
git commit -m "Initial site"
git push
```

## 3. Turn on GitHub Pages

1. In the repo, go to **Settings → Pages** (left sidebar, under "Code and
   automation").
2. Under **Build and deployment → Source**, choose **Deploy from a
   branch**.
3. Under **Branch**, choose `main` (or whichever branch has the files) and
   folder `/ (root)`. Click **Save**.
4. Wait 30–60 seconds. Refresh the page — GitHub shows a green box with
   your live URL, something like:
   `https://YOUR-USERNAME.github.io/boxed-up-games/`

That's it. No custom domain, DNS, or config needed unless you want one.

## 4. Confirm the live stats are working

Open the live URL (not a local file, not this chat preview — see below)
and check the **Kart for Brainrots** card and the dispatch board at the
bottom. Give it a couple of seconds on first load.

If a stat box shows a dash or "Couldn't reach Roblox — retry shortly":
1. Open the browser's dev tools (F12 or right-click → Inspect) →
   **Console** tab.
2. Reload the page and watch for red errors mentioning `games.roblox.com`,
   `allorigins`, or `codetabs`.
3. This almost always means the free CORS proxy currently in use has
   changed its terms again (this already happened once with corsproxy.io —
   see the note in the config below). Swapping in a different proxy from
   the `CORS_PROXIES` list near the top of the `<script>` block fixes it.

**Why it won't work here in the chat preview:** this conversation's
preview renders the page inside a sandboxed iframe that blocks a lot of
outbound network calls, including the CORS proxy requests the stats
depend on. That sandbox doesn't exist on a real GitHub Pages URL — it's a
limitation of previewing here, not a bug in the file.

## 5. Adding more games later

Open `index.html`, find the `GAMES` array near the top of the `<script>`
block (search for `const GAMES`), and add another entry:

```js
{
  name: "Your Next Game",
  tagline: "One short line about it.",
  placeId: 000000000,       // the number in roblox.com/games/PLACE_ID/...
  universeId: null,          // leave null — resolved automatically
  thumbnail: null,           // paste a data:image/... URI, or leave null
  private: false             // true hides stats with a "private" message
}
```

No other file needs to change. Commit and push (or re-upload) and the new
card appears automatically.

## 6. A note on the CORS proxies

Roblox's stats API doesn't allow direct browser requests from other
websites, so this page routes through free public CORS relays
(`allorigins.win`, `codetabs.com`) as a workaround. These are convenient
because they need no signup, no key, and no server of your own — but
they're run by third parties and occasionally change their limits or go
down, which will look like stats "breaking" with no code changes on your
end.

If that becomes a recurring annoyance, the durable fix is a small
serverless function you own (e.g. a free Cloudflare Worker) that calls
Roblox's API server-side and that this page calls instead of a public
proxy. That removes the dependency on someone else's free tier entirely.
Ask if you'd like that built.
