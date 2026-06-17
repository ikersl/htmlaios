# HTMLAIOS

A fake operating system where every screen is generated live by an LLM.

There's no pre-built UI. Every desktop, terminal, file manager window, and
settings panel is HTML generated on the fly by a language model, in response
to whatever you just clicked or typed. Three things are actually real and
persistent across screens:

- a small in-memory **filesystem** (a JS object of path → content)
- a **settings/state** object (theme color, scanlines, etc.)
- **real web search results**, when the model decides an action calls for one

Everything else — every window, icon, error message, and "crash" — is
re-hallucinated from scratch on each interaction.

## How it works

1. Open `index.html` in a browser and paste in your own API key
   (OpenRouter or direct Anthropic).
2. Every click inside the OS sends an action description (e.g. `"open
   terminal"`, `"delete file notes.txt"`) up to a small React app wrapping
   the OS.
3. That app sends the model the current screen's HTML, the settings state,
   and the full filesystem, and asks for four blocks back: updated `state`,
   filesystem operations (`fsops`), an optional `search` request, and the
   next screen's `html`.
4. If the model asks for a real search, it's actually run (via OpenRouter's
   web plugin, or Anthropic's hosted web search tool), and the model is
   called again with genuine results to render.
5. The generated screen is rendered inside a sandboxed iframe
   (`sandbox="allow-scripts"`, no same-origin access).

No backend, no build step — it's a single self-contained HTML file.

## Usage

Just open `index.html` in any modern browser. You'll need an API key from
one of:

- [OpenRouter](https://openrouter.ai/keys) — recommended, since it supports
  browser-side calls directly
- [Anthropic](https://console.anthropic.com/settings/keys) — direct calls
  from a browser will likely hit a CORS error unless you're using a proxy,
  since the Anthropic API doesn't enable CORS for browser requests

Your API key is kept in browser memory only for the current session. It is
never written to disk, sent to any server other than the provider you
chose, or persisted anywhere.

## Notes

- Expect occasional weirdness — broken layouts, inconsistent state,
  in-universe "crash" messages — that's the nature of a UI that's
  reconstructed from scratch every time rather than a real, stable program.
- Some embedded external sites/images may fail to load if they block
  iframe embedding (`X-Frame-Options` / CSP).
- API usage is billed by whichever provider's key you supply; this project
  does not include or proxy any keys of its own.

## License

MIT — see [LICENSE](LICENSE).
