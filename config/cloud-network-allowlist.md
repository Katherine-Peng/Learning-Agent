# Cloud run — network allowlist for the Learning Agent

> **Why this file exists:** On 2026-07-05 a full sweep confirmed that 38/40 source endpoints
> fail from the cloud (Claude Code on the web) environment with `CONNECT tunnel failed,
> response 403` — the **environment's egress network policy** blocks them. This is NOT
> YouTube/Substack rate-limiting a datacenter IP (the rss2json fallback proxy was blocked
> too, which a site-side block can't explain). The previous runs' "datacenter IP block"
> diagnosis was wrong. No fetch trick fixes this — only the environment settings can.

## How to fix (one-time, ~2 minutes)

In **claude.ai/code → Environments → (the environment this repo's scheduled task runs in)
→ Network access**, either:

- **Option A (simplest):** set network access to **Full / unrestricted**. This repo's agent
  only reads public feeds and writes to Notion/GitHub, so this is low-risk. **Recommended.**
- **Option B (tighter):** keep the allowlist mode and add the domains below.

Docs: https://code.claude.com/docs/en/claude-code-on-the-web

## Domains to allowlist (Option B)

Verified blocked on 2026-07-05; each maps to a source in `config/sources.md`.

### Critical — primary content sources
```
youtube.com            # all 13 YouTube channel RSS feeds (www.youtube.com/feeds/videos.xml)
substack.com           # Substack redirect/edge for all custom-domain feeds below
substackcdn.com        # Substack CDN (feed assets/redirects)
oneusefulthing.org     # Ethan Mollick (Tier 1)
simonwillison.net      # Simon Willison (Tier 1)
lennysnewsletter.com   # Lenny Rachitsky (Tier 1)
smashingmagazine.com   # Vitaly Friedman (Tier 1)
maggieappleton.com     # Maggie Appleton (Tier 1)
medium.com             # Google PAIR feed (Tier 1) + Aakash mirror
shapeof.ai             # Emily Campbell (Tier 1, manual check)
wattenberger.com       # Amelia Wattenberger (Tier 1, manual check)
```

### Tier 2 feeds
```
blog.google
openai.com
deepmind.google
huyenchip.com
interconnects.ai
jack-clark.net
magazine.sebastianraschka.com
cameronrwolfe.substack.com
latent.space
stratechery.com
newsletter.pragmaticengineer.com
creatoreconomy.so
exponentialview.co
jakobnielsenphd.substack.com
lukew.com
newsletter.uxdesign.cc
bradfrost.com
peterme.com
garymarcus.substack.com
langchain.com
news.aakashg.com
feeds.simplecast.com   # Hard Fork podcast feed
every.to               # Dan Shipper / AI & I
pair.withgoogle.com
aixdesign.co
normaltech.ai
```

### Useful extras
```
api.rss2json.com       # fallback feed proxy (only needed if a site later rate-limits the runner)
nngroup.com            # recurring discovered-pick source
arxiv.org              # recurring discovered-pick source
```

### Already reachable (no action)
```
microsoft.com / www.microsoft.com   # MS Research feed — worked all along
raw.githubusercontent.com          # Anthropic engineering community RSS mirror (stale, but reachable)
```

### Known special case
```
anthropic.com   # bypasses the session proxy entirely (NO_PROXY) and Anthropic's edge
                # blocks datacenter fetches with a real HTTP 403. Allowlisting may not
                # help; the agent should keep using WebSearch for anthropic.com articles.
```

## After changing the setting

The next scheduled run re-tests automatically: STEP 2's fetching discipline starts with a
one-call preflight and reports `policy-blocked` vs `OK` per lane in Source Health. If the
briefing still says `policy-blocked` after you've updated the environment, the change
didn't apply to the environment the task actually runs in — check which environment the
scheduled task uses.
