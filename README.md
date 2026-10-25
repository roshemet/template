# Your bank agent on GitHub (Roshemet)

This repo, in your own GitHub account, runs your bank agent once a day on GitHub's computers. It signs in to your
banks and card companies with your logins and sends the transactions to **your own app**, which asks you to approve
them (✅ Save / ❌ Discard). Nobody else, us included, gets your logins or your transactions.

**Keep this repo private.** The agent does not run in a public repo: anyone could read its runs' logs.

## What is in it

| | What it is |
|---|---|
| `.github/workflows/agent.yml` | **Bank run**: every day at 07:00 UTC (10:00 Israel time in summer, 09:00 in winter; GitHub may start it later), never on Shabbat or Yom Tov |
| `.github/workflows/update.yml` | **Update the agent**: installs another release of the agent, after checking where it was built |
| `roshemet.json` | The agent's version that runs |
| Secrets (Settings → Secrets and variables → Actions) | `LOGIN_<COMPANY>`: one per bank or card company, its login's fields. `ROSHEMET_TOKEN`: this repo's own pairing with your app (it can only send scrapes for approval) |
| Variables (same page) | `ROSHEMET_APP`: your app's address. `ROSHEMET_MAP`: which account goes where in the app (company and last 4 digits) |

GitHub keeps the secrets encrypted and gives them only to the Bank run step. Before printing anything, the agent
tells GitHub to show every value of every login as `***` in the log.

**Please do not edit `agent.yml`.** It is what keeps the logins to that one step; a change there could expose them.

## Setting it up

From the computer or box that already keeps your logins (the agent, version 1.1.0 or newer):

```bash
node scrape.mjs github-connect --repo <you>/<this repo> --code <the code from your dashboard>
sudo finance-scraper github-connect --repo <you>/<this repo> --code <code>     # on a box
```

The code comes from your app: Upload tab → Bank scrape → **Pair a scraper box**. The command asks for a GitHub token
once (it shows a link that fills it in; you pick this repo), puts each login here sealed with this repo's key, pairs
this repo with your app, then runs a check on GitHub and a first run, and says what each found. The token is kept
nowhere. (The setup app will do this for you behind its own screens.)

## Running it now, and reading a run

**Actions → Bank run → Run workflow.** Days back: 45 is usual; **0 only checks** (the repo is private, the logins and
the map are here, your app accepts this repo, the browser starts) without signing in to any bank.

- **Green:** the run reached your app. Approve it there.
- **Red:** open the run. Its page shows a table: each login, ✓ or the reason. `BLOCKED` means the bank refused
  GitHub's computer: it is not your password. Upload that card's file from its website meanwhile.
- Your app also tells you if a day's run did not arrive, with a link back here.

## GitHub's risk, plainly

GitHub's terms limit what its computers may be used for. GitHub may stop these runs, and in rare cases suspend the
account. Keep a second way: your own computer running the agent, or uploading files from the bank.

## Updating, and stopping

- **Update:** Actions → Update the agent → Run workflow, with the version from
  [the releases](https://github.com/roshemet/agent/releases). Going back works the same way.
- **Pause:** Actions → Bank run → `…` → Disable workflow.
- **Stop for good:** delete the secrets (Settings → Secrets and variables → Actions), revoke the pairing in your app
  (Upload tab → Bank scrape), and delete this repo.
