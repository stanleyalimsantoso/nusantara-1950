# Nusantara 1950: your own website

This folder is the whole game. It runs on any website. You'll do five things:

1. Try it on your own computer.
2. Add your music.
3. Put it online for free with GitHub Pages.
4. Set up free online play with Firebase.
5. Play with friends.

Each part works on its own: after part 3 you can already play against the bots online, and part 4 adds playing against friends.

```
nusantara-1950-site/
├── index.html          the game (don't edit by hand; ask Claude for changes)
├── config.js           your Firebase settings (part 4)
├── firestore.rules     database security rules (part 4)
├── README.md           this guide
└── music/
    ├── playlist.json   which MP3 plays in which mood (part 2)
    └── (your .mp3 files)
```

---

## 1. Try it on your computer

Double-clicking `index.html` opens the game, but the browser won't let the page read `music/playlist.json` from a plain file, so you'd only hear the built-in music. Run a tiny local web server instead. You already have Python:

1. Open a terminal in this folder.
2. Run `python3 -m http.server 8000` (on Windows: `python -m http.server 8000`).
3. Open <http://localhost:8000> in Chrome.
4. Press **Start practice game**. Stop the server with Ctrl+C when you're done.

---

## 2. Add your music

### Put the files in

1. Copy the MP3s into the `music/` folder. Simple names without spaces are easiest: `gamelan-night.mp3`.
2. Open `music/playlist.json` in a text editor (VS Code, Notepad, TextEdit in plain-text mode).
3. List each file under the mood where it should play:

| Mood | When it plays |
|---|---|
| `calm` | The hunt has just started |
| `tension` | Unrest is spreading, or the first outpost exists |
| `war` | The moment civil war is declared |
| `invasion` | The Dutch army has joined the war (uses `war` if empty) |
| `ending` | Someone has won |

Example: list MP3 filenames under each mood. No extra metadata is needed:

```json
{
  "calm": [
    "gamelan-night.mp3",
    "keroncong-evening.mp3"
  ],
  "tension": [],
  "war": [
    "march-of-freedom.mp3"
  ],
  "invasion": [],
  "ending": []
}
```

JSON rules that trip people up:
- Text goes in double quotes `"like this"`.
- Items are separated by commas, but there's **no comma after the last item** in a list.
- Check your file with a validator such as <https://jsonlint.com> if the music doesn't change.

How the player behaves:
- When the mood changes, the music **crossfades straight to the new mood**. Inside a mood, tracks play in a shuffled order.
- A mood with no files uses the built-in synthesized music, so you can fill in moods one at a time.
- Hovering over the Music button shows the filename. Optional objects with `file`, `title` and `artist` still work and appear under **Music credits** in the game menu.
- A misspelled or missing file is skipped, so the game never breaks. If a track never plays, check its spelling.

Test with the local server from part 1.

---

## 3. Put it online with GitHub Pages (free)

1. Create a free account at <https://github.com>.
2. Click **+** (top right) → **New repository**. Name it `nusantara-1950`, choose **Public**, and click **Create repository**.
3. On the new repository page, click **uploading an existing file**. Drag in **everything in this folder**, including the `music` folder, then click **Commit changes**.
4. Go to **Settings** → **Pages**. Under **Build and deployment**, set Source to **Deploy from a branch**, Branch to **main** and **/ (root)**, then click **Save**.
5. Wait a minute and refresh. Your game is at `https://YOUR-USERNAME.github.io/nusantara-1950/`.

Practice mode against bots works now. For online games with friends, do part 4.

**Updating later:** to change a file (new music, a new `index.html` from Claude), open it on GitHub, or use **Add file → Upload files** to replace it, then commit. The site updates within a minute or two.

---

## 4. Online play with Firebase (free)

Firebase is Google's free real-time database. It passes moves between players' browsers.

1. Go to <https://console.firebase.google.com> and sign in with a Google account.
2. **Create a project** named `nusantara-1950`. You can turn off Google Analytics.
3. **Turn on anonymous sign-in**, so players get a private ID without making an account: in the left menu, **Build → Authentication → Get started → Sign-in method → Anonymous → Enable → Save**.
4. **Allow your website:** still in Authentication, open **Settings → Authorized domains → Add domain**, and add `YOUR-USERNAME.github.io`.
5. **Create the database:** **Build → Firestore Database → Create database**. Choose a location close to you and your friends: `australia-southeast1` (Sydney) or `asia-southeast1` (Singapore) both work well from Perth. Start in **production mode**.
6. **Paste the security rules:** in Firestore, open the **Rules** tab, replace everything with the contents of `firestore.rules` from this folder, and click **Publish**.
7. **Get your settings:** click the gear icon → **Project settings**. Under **Your apps**, click the **Web** icon (`</>`), give it a nickname, and register it. Firebase shows a `firebaseConfig = { ... }` block.
8. Open `config.js` and replace the placeholder values with yours. Keep `window.NUSANTARA_FIREBASE =` at the start. Save the file, then upload it to GitHub (part 3, updating).
9. Open your game website. Under **Play online**, you should now see the room controls and seats. If it still says online play isn't set up, see troubleshooting below.

Is it safe to put these settings on a public website? Yes. Every visitor's browser needs them to connect, so they're not secret. What protects the data is the rules you pasted in step 6.

The free plan allows tens of thousands of reads and writes per day. A game uses a few hundred, so you won't come close.

---

## 5. Play with friends

1. Open your game site and click **New private room**. The address bar now ends in something like `#r-k3v6q2`.
2. Send your friend the **full link, including the `#...` part**.
3. One of you clicks **Create an online game**. Each player clicks **Take seat**. For the Dutch, click **Use a bot** if you're only two.
4. Everyone clicks **Enter the game**.

Your friend doesn't need a claude.ai account, or any account at all.

---

## Troubleshooting

Press **F12** in Chrome and open the **Console** tab: error messages there usually point straight at the problem.

| Problem | Likely cause |
|---|---|
| "Online play isn't set up here" | `config.js` still has the placeholders, or wasn't uploaded |
| An error about `auth/unauthorized-domain` | Step 4 of part 4: add your github.io domain |
| `permission-denied` | The rules from step 6 weren't published |
| The music never changes | `playlist.json` has a typo (check it with jsonlint), or the file names don't match exactly; capitals matter |
| Changes don't show up | GitHub Pages takes a minute or two; reload with Ctrl+Shift+R |
