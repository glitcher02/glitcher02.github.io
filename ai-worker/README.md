# AI proposal drafter — backend Worker

The itinerary demo works fully offline (scripted, from built-in building blocks).
This optional Cloudflare Worker upgrades **one** demo — the AI proposal drafter —
so it calls OpenAI for real, without ever exposing your API key.

```
site (static, public)  ──POST──▶  this Worker (holds the key)  ──▶  OpenAI API
```

## Why a Worker (and not just client-side JS)
A static site's JavaScript is fully visible to visitors. An API key in that JS
could be copied and abused, billing your account. The key must stay server-side.
This Worker is that server side — it's the only thing that sees the key.

## Deploy (about 5 minutes)
From this folder (`ai-worker/`):

```bash
# 1. One-time: install + log in to your Cloudflare account
npx wrangler login

# 2. Set your OpenAI key as an encrypted secret (you paste it; it's never committed)
npx wrangler secret put OPENAI_API_KEY

# 3. (Recommended) add a per-IP daily cap so a public demo can't run up a bill
npx wrangler kv namespace create RL
#   → paste the printed id into wrangler.toml under [[kv_namespaces]] and uncomment

# 4. Ship it
npx wrangler deploy
```

`wrangler deploy` prints a URL like `https://archie-ai-drafter.<you>.workers.dev`.

## Connect the site to it
Open [`../demos/ai-brief/index.html`](../demos/ai-brief/index.html), find:

```js
const AI_ENDPOINT = ""; // paste your Worker URL here to switch on real AI
```

Paste the Worker URL between the quotes and redeploy the site. That's it — the
demo now drafts with OpenAI. Leave it empty and the demo stays on the built-in
scripted version (still fully functional).

## Notes
- **Model:** `gpt-4o-mini` (cheap + fast, great for a live demo — comfortably covered
  by free/low-cost usage). To use richer prose, change `MODEL` in `worker.js` to
  `gpt-4.1-mini` (or another model you have access to) and redeploy.
- **Strict guidelines:** the system prompt locks the model to writing luxury travel
  itineraries only, ignores attempts to change its instructions, and refuses off-topic
  input. Output is forced to a strict JSON schema so the page always gets a valid shape.
- **Cost control:** `max_tokens` is capped at 1500, CORS is locked to your domain,
  input length is clamped, and the optional KV cap limits requests per visitor per day.
- **No personal data:** the demo only sends a destination + a few preferences. Don't
  change it to accept real client details — this endpoint is public.
- **Custom domain (optional):** you can route this at e.g. `api.archiebutler.co.uk`
  via a Workers route in the Cloudflare dashboard instead of the `workers.dev` URL.
