# Render ↔ Calico Tabbycat Sync

A per-round sync tool between a Render-hosted Tabbycat instance (used for
draw generation) and a Calico-hosted Tabbycat instance (the public
tournament judges/teams interact with).

## Deploy your own copy

1. Click **Deploy to Render**

   [![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy)

2. Render reads `render.yaml` and asks for five values:
   - `RENDER_URL`: your Render Tabbycat instance's tournament URL. Both
     `https://your-instance.onrender.com/your-slug` and
     `https://your-instance.onrender.com/api/v1/tournaments/your-slug`
     work, trailing slash or not, the app normalizes it either way.
   - `RENDER_TOKEN`: your API token for that instance
   - `CALICO_URL`: your Calico tournament's URL, same deal, full API path
     or just `https://your-instance.calicotab.com/your-slug`
   - `CALICO_TOKEN`: your API token for that instance
   - `APP_PASSWORD`: a shared password to gate access to the tool

3. Click deploy. You'll get a URL for your own private sync tool. It
   asks for `APP_PASSWORD` on first load and again on every refresh.

## Using it, once per round

Open the URL, unlock with your password, and work through the sidebar:

0. **Overview**. Team/adjudicator counts on both instances,
   `team_map.json` backup and restore, dummy adjudicator/room creation.
1. **Import Teams**. Run once, before Round 1.
2. **Sync Team Availability**. Before generating each round's draw,
   pulls checked-in teams from Calico and marks them available on
   Render. Clear team availability on Render's UI first, Tabbycat's API
   500s otherwise.
3. **Push Draw**. Normally Render to Calico: review the draw on
   Render's UI, tick the box, push it to Calico as a Draft. Switch
   direction to Calico to Render if the draw was generated or
   hand-adjusted on Calico instead (a random R1, hand-fixed pullups,
   etc). Either way the destination round gets marked Draft, and any
   pairing that failed to push gets listed for you to fix manually.
4. **Pull Results → Render**. After results are confirmed on Calico.
   Pairings already confirmed on Render get skipped, and any pairing
   that failed to write gets listed for manual entry.
5. **Compare Standings**. Asks for your password again, then compares
   rank and key metrics between the two instances.
6. **Action Log**. Everything done through the app, imports, syncs,
   pushes, pulls, logins, plus any failures from Sections 3 and 4,
   newest first. Downloadable as `action_log.json`.

## Local development

```bash
cp .env.example .env   # fill in your real values, never commit this file
pip install -r requirements.txt
streamlit run app.py
```

## Notes

- `team_map.json` comes from Section 1 and gets used everywhere else to
  map team IDs between the two instances. It's gitignored, it's local
  data, not code.
- `team_map.json` and `action_log.json` live on Render's local disk,
  which doesn't survive redeploys or restarts. Download both now and
  then from their sections.
