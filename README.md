# Tiny World: add your city

Tiny World is a world map of the tiny, isometric city maps people have made of their own cities.
Zoom into a city and its map opens **live**, out of its spot on the globe, so you can step inside it.
Zoom back out and you return to the world.

This repo is just where cities get listed (the website lives elsewhere).
**To add yours, [open an "Add a city" issue](../../issues/new?template=add-city.yml).**

## Requirements

Your map is loaded inside an `<iframe>`, so:

1. **It must allow being embedded.** It must not send `X-Frame-Options: DENY` or `SAMEORIGIN`. A
   `Content-Security-Policy` must not restrict `frame-ancestors` (leave it out, or use `frame-ancestors *`).

   ```sh
   curl -sI https://your-site/ | grep -iE 'x-frame-options|frame-ancestors'   # no output = fine
   ```

   Vercel, Netlify, Cloudflare Pages and GitHub Pages don't add these by default. Some frameworks,
   CDNs and security plugins do.
2. **It's public, on HTTPS, and needs no sign-in.**
3. **It works inside a frame.** No frame-busting, no `window.top` redirects, nothing that only works
   as a top-level tab.
4. **It maps a real place:** a city, a region or a country.

Sites that can't be embedded aren't shown. Tiny World only lists maps you can step into.

## Nice to have

- **The camera protocol below.** Your map lines up with the real streets, and zooming out of it
  takes visitors back to the world.
- **A good opening view:** a recognisable part of the centre, at city-district zoom.
- **Sound off by default.** Tiny World blocks autoplay, but a page can't mute another site, so
  anything that plays after a click is up to you.
- **A clean embed mode** (say `?embed=1`) that hides your own chrome. Mention it in the issue.

## Camera protocol (optional, recommended)

Talk to the page that frames you with `postMessage`. Use real-world coordinates and a standard
web-map zoom (Leaflet/Google style, 256 px tiles).

**Report your view** whenever it settles, for example 300 ms after the last change:

```js
if (window.parent !== window) {
  const report = () => window.parent.postMessage({ tipo: "tiny_vista", lat, lon, zoom }, "*");
  // call report() after pans and zooms settle, and once when the map first appears
}
```

**Accept a view** and move there:

```js
addEventListener("message", e => {
  const m = e.data;
  if (!m || m.tipo !== "tiny_ir") return;
  flyTo(m.lat, m.lon, m.zoom ?? currentZoom, m.dur ?? 0.6);   // clamp to your own limits, that's fine
});
```

Tiny World sends `tiny_ir` to line your map up with the city under it. It reads `tiny_vista` to know
where visitors went, and when they've zoomed out past where they came in, it hands them back to
the world map.

(This is the protocol [Tiny SP](https://tiny-sp.agenturl.dev/) speaks, so you can see it working there.)

## What happens after you submit

A maintainer checks that the site can be embedded, draws your city's landmark icon, lines your map
up on the globe, and adds it to the world. Sites built on a framework Tiny World already knows
(tinyrj's, or Tiny Barcelona's) can get the full treatment: your map's own art laid on the real
map, and a tilt into your camera when stepping inside.
