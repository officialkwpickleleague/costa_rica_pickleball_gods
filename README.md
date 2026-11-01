# Costa Rica Trivia

A live multiplayer trivia game (Costa Rica edition). The host shows questions on a big screen, players join on their
phones with a PIN and answer as fast as they can. Correct answers earn 1000 points for an instant answer, falling to
500 at the buzzer.

Answer colours, in order: **blue (▲), white (◆), red (●), gold (■)**. The shapes also tell the answers apart.

## Folder layout

Keep this structure. The pages load files by these exact paths.

    costa_rica_trivia/
      index.html          player page (phones)
      host.html           host page (big screen)
      config.js           Firebase settings (leave as null for demo mode)
      README.md
      css/style.css       all colours and layout
      img/logo.png        howler monkey mascot (you add this)
      img/favicon.png     browser tab icon (you add this)
      js/common.js        shared helpers + the sample quiz
      js/db.js            database adapter (Firebase or demo mode)
      js/diag.js          shows a readable error instead of "Loading…"
      js/host.js          host screens and game logic
      js/player.js        player screens
      js/vendor/qrcode.js QR code library

## Try it on one computer first (no setup)

1. Open **Command Prompt** (or PowerShell) and go to the game folder:

       cd C:\Users\migso\Desktop\costa_rica_trivia

2. Start a tiny web server (needs Python installed):

       python -m http.server 8000

   If `python` is not recognised, try `py -m http.server 8000`.

3. Open http://localhost:8000/host.html, click **Start a game**, then open http://localhost:8000/index.html in more
   tabs and join with the PIN.

This "demo mode" only syncs between tabs of the same browser. Opening the files by double-clicking them will not
work, because the game uses JavaScript modules that browsers only allow over http(s).

## Things to change by hand (Costa Rica edition)

1. **Images:** put the howler monkey photo in `img/` as `logo.png` and a small square version as `favicon.png`.
   Keep those names, or search the files for `img/logo.png` / `img/favicon.png` and change them.
2. **Titles:** the page titles and header still say "Quiz Live". Edit these spots:
   - `index.html` and `host.html`: the `<title>` line.
   - `js/host.js`: search for `Quiz Live · Host` (editor header).
3. **Questions:** edit them in the browser on `host.html` (tick the circle for the correct answer). To change the
   built-in starter quiz, edit `DEFAULT_QUIZ` in `js/common.js`. Use **Export** to save your finished quiz as a
   `.json` file and **Import** to load it again, for example on another computer.
4. **Colours:** the palette is at the top of `css/style.css` (`--blue`, `--red`, `--gold`, and `--o0` to `--o3` for the
   four answer colours).

## Saved data (kept separate from the pickle game)

This game saves its data in the browser under names beginning with `costarica_trivia_`:

- `costarica_trivia_host_quiz` - the quiz you are editing
- `costarica_trivia_host_session` - the game in progress on the host
- `costarica_trivia_player_session` - a player's place in a game (so a refresh rejoins)
- `costarica_trivia_demo_db` - demo-mode game data

Because the names differ from the old pickle game's (`quizlive_...`), both games can be served from the same address
(for example `localhost:8000`) without overwriting each other's quizzes. The quiz is saved per browser, so use
**Export** before clearing browser data or switching computers.

If you use Firebase, games are stored under the `games` path of the database. Use a new Firebase project for this
game, or at least a different Realtime Database, if you do not want the two games sharing one database.

## Go live (so phones can join)

### 1. Create a free Firebase database (about 5 minutes)
1. Go to https://console.firebase.google.com, sign in with a Google account, **Add project** (turn Analytics off).
2. In the left menu: **Build -> Realtime Database -> Create database**. Pick the closest location and choose
   **Start in locked mode**.
3. Open the **Rules** tab, paste the following, and click **Publish**:

       {
         "rules": {
           "games": { ".read": true, ".write": true }
         }
       }

   (Anyone with a game PIN can read/write that game. Fine for friendly games; not for sensitive data.)
4. Click the gear icon -> **Project settings** -> under **Your apps** click the web icon `</>`, register an app
   (skip hosting), and copy the `firebaseConfig = { ... }` values.
5. Open `config.js` and replace `export const firebaseConfig = null;` with
   `export const firebaseConfig = { ...the values you copied... };`
   Make sure the `databaseURL` line is included. If it is missing, the Realtime Database page shows the URL at the top.

### 2. Put it on GitHub Pages
1. Create a new GitHub repository (public) and upload everything in the `costa_rica_trivia` folder (keep the folder
   structure).
2. Repo **Settings -> Pages -> Build and deployment**: Source = *Deploy from a branch*, Branch = `main`, folder
   `/ (root)`. Save.
3. After a minute your game is at `https://YOUR-NAME.github.io/REPO-NAME/`.
   - Host opens `.../host.html` (on a laptop connected to a projector/shared screen)
   - Players open `.../` (or scan the QR code on the host screen)

The Firebase `apiKey` in config.js is meant to be public; the Rules above are what control access.

## Running a game
1. On `host.html` edit your questions (tick the circle for the correct answer, choose the time limit, reorder or
   duplicate questions). Changes save automatically in that browser. Use **Export** / **Import** to keep several quizzes.
2. **Start a game**, share the PIN or QR code, wait for players, **Start quiz**.
3. Each question ends when the timer runs out or everyone has answered. Click **Next question** after the leaderboard.
4. At the end click **Finish & clear game data** to delete the game from the database.

Notes: players can only join while the game is in the lobby. If the host refreshes the page, the game resumes.
The correct answers are kept on the host's device and are not sent to players until the reveal.

## Troubleshooting

- **"Loading…" forever or "Something went wrong":** a file is missing or in the wrong folder. The message names the
  file it could not load. Check it against the folder layout above.
- **QR code is blank on the host screen:** `js/vendor/qrcode.js` is missing.
- **Mascot picture missing:** check `img/logo.png` exists and the name is spelled exactly like that.
- **Phones cannot join:** demo mode only works inside one browser. Set up Firebase and host the game online (see
  "Go live").
