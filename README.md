# Fishing Chart, README

A web app for planning trips out of Coecles Harbor, Shelter Island, around Montauk and Block Island. Fishing spots, the real boat route from Coecles, distance and run time tools, and live NASA chlorophyll and water temperature overlays.

The folder has three files:
- index.html, the app itself (one self contained file)
- CLAUDE.md, project context for Claude Code
- README.md, this file

---

## The one thing to know first
This app needs an internet connection. It cannot run as a local file opened from the iPhone Files app, because iOS opens local HTML in a sandbox with no internet and the map comes up blank. That is why it gets hosted at a web link. Once it is at an https link, it works everywhere: Mac, iPhone Safari, and as a home screen app.

---

## Fastest way to just see it work (on the Mac)
Double click index.html. It opens in your default browser with the full chart and all layers. Desktop browsers do not sandbox local files the way the iPhone does, so this always works as a quick check.

---

## Deploy it to a permanent link (with Claude Code)
1. Put index.html and CLAUDE.md in a folder, for example fishing-app.
2. Right click the folder, choose New Terminal at Folder.
3. Type claude and press return.
4. Paste this:

   I have a single self contained file, index.html, in this folder. It is a Leaflet
   fishing chart that pulls map tiles and NASA satellite overlays from the internet.
   Do two things: first start a local server so I can open it in my browser and confirm
   every layer works. Then deploy it to a permanent, free, public URL I can open on my
   iPhone and share with a friend. Walk me through any login or account steps one at a
   time. I am not a developer, so keep it simple and tell me exactly what to click.

5. Claude Code will preview it locally, then pick a free host (GitHub Pages, Netlify, Cloudflare Pages, or Vercel), handle the deploy, and give you a link.

---

## Put it on your iPhone as an app
1. Open the link in Safari on the iPhone.
2. Tap the Share icon.
3. Tap Add to Home Screen.
4. It becomes an app icon that launches full screen. Text the same link to anyone you want to share it with.

---

## Using the app
- Find a spot: type in the search box to fly to any spot.
- Route from Coecles: pick a spot and it draws the real route (around the south side of Gardiners Island) with distance in nautical and statute miles and run time.
- Chlorophyll and Water Temp buttons: drop the NASA satellite layer on top. If a layer looks blank, roll the Sat date back 2 to 4 days (clouds and publish lag).
- Measure: tap two points on the water for distance, true bearing, and run time.
- Layers menu (top right): toggle inshore spots, offshore spots, range rings from Coecles and Montauk, and the route corridor.
- Speed box: set your cruise speed in mph; all run times update.

---

## Good to know
- Distances are planning estimates in nautical and statute miles. Bearings are true, not magnetic (local variation about 13 degrees west). Not for navigation; verify on your chartplotter.
- Satellite overlays are coarse (a few km per pixel), meant for spotting temperature and color breaks where bait and tuna hold, not fine detail.
- To make changes later (new spots, a trip planner, corridor tweaks), just tell Claude Code in plain English in this folder. CLAUDE.md gives it all the context it needs.
