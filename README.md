# midi-device-debug

Tiny page for testing whether the browser can see a USB MIDI device via the Web MIDI API.

## Run locally

```bash
python -m http.server 8000
```

Then open <http://localhost:8000/> in Chrome or Edge and click **Request MIDI access**.

`localhost` counts as a secure context, so the API works without HTTPS.

## Browser support

- Chrome, Edge, Opera: native
- Safari 16.4+: native
- Firefox: behind `dom.webmidi.enabled` flag

## Deploy

Push to `main` and enable GitHub Pages (Settings → Pages → Source: `main` / root). The site will be served over HTTPS, which Web MIDI requires.
