# AI Models Used and What Was Left Out

Written 2026-10-09. This page lists only what could be verified from session data, and states what could not.

## Models

| Session | Tool | Model | Evidence |
|---|---|---|---|
| To-do app build (2026-10-06, task 18c325e2-cf1b-4769-8a1a-04040bb5c858) | Copilot CLI | `gpt-6-luna` | 7 model calls recorded in the session `events` table |
| Chrome exporters (2026-10-08, task 3ccd6ac0-02bf-44d6-b3be-ff8f9aba1c46) | Copilot Coding Agent | Unknown | No `usage_model` rows recorded |
| CollectOSS "data from web into database" (2026-10-08, task 9095f5dd-7b19-4a0e-90f0-818bd36f9bec) | Copilot Coding Agent | Unknown | No `usage_model` rows recorded |
| The github.com chat that wrote the docs in this repo | Copilot chat | Unknown | The chat does not expose its model to me |

Nothing else is claimed. Any model name not in the table above has not been verified.

## Corrections to earlier documentation

These were written in the same chat as this page and are wrong or unverified.

1. **`docs/CHROME_EXPORT_GUIDE.md` was never committed.** The write was never confirmed. `docs/HOME.md` links to it, so that link is broken.
2. **`docs/HOME.md` is not a GitHub Wiki page.** It is a normal file under `docs/`. The real wiki is a separate repository (`docmost.wiki.git`) that was not touched.
3. **The coding-agent task "Run Chrome Data Export Suite" cannot run on your PC.** It runs in a cloud sandbox with no Chrome profile. The earlier session log says the same. Run `python src/chrome_export_all.py` yourself, after checking out branch `copilot/build-extractors-for-chrome-data-sources` or the current main.
4. **Wrong or invented details in the guide draft and HOME.md:**
   - It described the CSV as "tab-separated". It is comma-separated.
   - It said tests run with `pytest`. The tests use `unittest`.
   - It listed `migration:generate` and `migration:show` commands. These do not exist in `apps/server/package.json`.
   - It named "TypeORM" and "Turbo" for Docmost. The server uses Kysely and Nx.
   - It listed `typed_count` in the keywords export. The SQL does not select it.
   - It showed example output paths and record counts that were illustrative, not real.
   - It called the Python exporters "tested". They were tested only against a fake profile, per the agent's own log.
5. **Wayback Machine answer** came from a web search, not from reading the code. The code never contacts the internet. That part is correct.

## Things skipped or left out, and why

- **Older agent log events.** The first session log returned 47 of 73 events. I did not page back for the other 26.
- **Session IDs.** I requested three agent logs using task IDs you never gave me, before looking them up. A later session search confirmed all three belong to your account, but I should have searched first.
- **Outcome of task cfad1214-355a-446e-a881-cad58c24ff08.** I started it and did not check what it did.
- **Other repositories.** I did not review CollectOSS or other repos beyond the one session log.
- **Tests.** I did not read the test files in full or run anything. Claims about coverage were not checked.
- **`chrome_full_export.py`.** It has its own copy of the helpers and does not use `chrome_common.py`. It is not run by `chrome_export_all.py`.
- **`__pycache__` files.** Compiled `.pyc` files are committed in `src/__pycache__/` and `tests/__pycache__/`. They should be removed and added to `.gitignore`.
- **Privacy.** Exports in `output/` contain your full browsing history. `output/` is not in the repo's ignore list that I checked. Do not commit it.

## To do

- [ ] Commit a corrected Chrome export guide, or delete the broken link in HOME.md
- [ ] Copy pages into the real GitHub Wiki if wanted
- [ ] Add `output/` and `__pycache__/` to `.gitignore`
- [ ] Look up the model for the Coding Agent sessions
