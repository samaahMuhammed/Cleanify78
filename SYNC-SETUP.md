# Just One: syncing

## Easiest for roommates: invite-link home sync

**One-time setup (one person, about 5 minutes)**
1. Go to console.firebase.google.com, sign in, **Create a project** (any name, Analytics off).
2. Build → **Realtime Database** → Create Database → any location → **locked mode** → Enable.
3. **Rules** tab → replace everything with the line below → **Publish**:
   `{ "rules": { "homes": { "$id": { ".read": true, ".write": true } } } }`
4. **Data** tab → copy the address at the top (like `https://xxxx-default-rtdb.firebaseio.com`).
5. In the app: Settings → Home sync → paste the address → Save → **Turn on home sync**.

**Then, every roommate**
Board tab → **Copy invite link** → send it. They open it, type their name, tap **Join Home**. No accounts, no setup. The database address is inside the link.

**Privacy:** house data is encrypted on your device (AES-GCM) before upload. The key and address travel only in the part of the link after `#`, which browsers never send to any server. Anyone with the link can read and edit the home, so share it only with roommates. Records can only be reached by their random id; the rules above do not allow listing.

Free tier: Firebase's free plan is far more than a household needs.

---

# Optional: GitHub sync (advanced)


The app is one file (`index.html`). GitHub plays two roles:

| Repo | Visibility | Purpose |
|---|---|---|
| `just-one` (the app) | Public | Hosts `index.html` on GitHub Pages |
| `just-one-data` (the cloud) | **Private** | Stores `data/house.json` and `data/me-<id>.json` |

Two repos because GitHub Pages needs a public repo on free accounts, and your chat, lists and recipes should not be public. The app refuses to connect to a public data repo.

## One-time setup (about 5 minutes)

1. **App repo.** Create a public repo `just-one`, upload `index.html` to it. Settings → Pages → Deploy from branch → `main` / root. Your app lives at `https://<you>.github.io/just-one/`.
2. **Data repo.** Create a **private** repo `just-one-data` and tick "Add a README" so it has a first commit.
3. **Token.** github.com/settings/personal-access-tokens → Generate new token (fine-grained):
   - Repository access: **Only select repositories** → `just-one-data`
   - Permissions → Repository → **Contents: Read and write**
   - Copy the token (`github_pat_...`).
4. **Connect.** Open the app → ⚙️ Settings → Sync with GitHub (or "Sync with GitHub" on the first screen). Enter `you/just-one-data`, paste the token, Connect.

## More devices (yours)

Open the app URL, connect with the same repo and token, and **set Profile ID to the one shown on your first device** (Settings → Sync). Everything arrives within seconds.

## Roommates

Add each roommate as a collaborator on `just-one-data` (Settings → Collaborators, write access). Each makes their own token (step 3, same repo) and keeps their **own** Profile ID. They join your home with the invite code from the Board tab.

## How it behaves

- Works offline; changes sync when you are back online.
- Changes push about 1.5 seconds after you make them; every device checks for others' changes every 20 seconds and whenever the tab regains focus.
- Edits made at the same moment on two devices are merged item by item (a deletion on one device and an addition on another both survive). If the very same field is edited on both, the latest push wins.
- Each sync is a git commit, so the repo history is your backup and undo.

## Good to know

- The token lives only in that device's browser storage, never in the repo. The Gemini key is also never synced.
- Anyone with access to the data repo can read everything, including items set to "private kitchen" (that setting only hides things inside the app).
- To disconnect a lost device, delete its token on GitHub.
