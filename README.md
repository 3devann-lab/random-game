# random-game

**Cafeteria & Backrooms: Lunch Rush 3D**: one big `index.html` with several levels, including **DOORS mode**: a 100-room hotel run with Rush, Ambush, Screech, Eyes, Halt, Alex chases, Mrs. C's Library and the Room 100 finale.

DOORS mode can be played **solo** or **with friends online**. Multiplayer uses **Google Firebase (Realtime Database)**. It's free, and everything still lives in `index.html`.

---

## ▶️ Playing

1. Open `index.html` in Chrome, Edge or Firefox. You can double-click it, or host it anywhere (see "Sharing with friends" below).
2. Pick **🚪 5. DOORS** and press start.
3. **Solo:** press **ENTER THE HOTEL**.
   **Multiplayer:** press **👥 PLAY WITH FRIENDS**.

### Controls

| Key | Action |
| --- | --- |
| WASD | move |
| Space | run |
| Shift / C | crouch |
| E | interact / open doors / hide |
| 1-8 | pick item |
| F / click | use item |
| Esc / P | pause (in multiplayer the game keeps going) |
| **T** (or Enter) | chat *(multiplayer)* |
| **Q** | ping the thing you're looking at *(multiplayer)* |
| **G** | wave *(multiplayer)* |
| ← → / click | switch who you spectate when dead *(multiplayer)* |

---

## 🔥 Setting up multiplayer (Firebase)

You do this **once**, and it takes about 5 minutes. You need a Google account. The free "Spark" plan is enough; no credit card is required.

### 1. Create a Firebase project
1. Go to <https://console.firebase.google.com> and click **Create a project** (or **Add project**).
2. Give it any name, like `doors-with-friends`. Google Analytics can be turned **off**.
3. Click **Create project** and wait for it to finish.

### 2. Turn on the Realtime Database
1. In the left menu open **Build → Realtime Database**. (Use **Realtime Database**, not "Firestore".)
2. Click **Create Database**.
3. Pick a location (any is fine) and press **Next**.
4. Choose **Start in test mode** and press **Enable**.

### 3. Set the database rules
Test mode switches itself off after 30 days. Set rules that keep working:

1. In **Realtime Database**, open the **Rules** tab.
2. Replace everything with this and press **Publish**:

```json
{
  "rules": {
    "doorsMP": {
      ".read": true,
      ".write": true
    },
    ".read": false,
    ".write": false
  }
}
```

This lets the game read and write only inside `doorsMP/`. Anyone who has your config could write there, which is fine for a game you only share with friends.

### 4. Get your web config
1. Click the ⚙️ gear next to **Project Overview**, then **Project settings**.
2. Scroll to **Your apps** and click the **`</>`** (Web) button.
3. Give the app any nickname and click **Register app**. You don't need Firebase Hosting.
4. Firebase shows a block like this. Copy it:

```js
const firebaseConfig = {
  apiKey: "AIza....",
  authDomain: "doors-with-friends.firebaseapp.com",
  databaseURL: "https://doors-with-friends-default-rtdb.firebaseio.com",
  projectId: "doors-with-friends",
  storageBucket: "doors-with-friends.appspot.com",
  messagingSenderId: "1234567890",
  appId: "1:1234567890:web:abcdef123456"
};
```

> If there's **no `databaseURL` line**, go back to **Realtime Database**. The URL is shown at the top of the **Data** tab (for example `https://xxxx-default-rtdb.europe-west1.firebasedatabase.app`). Add it to the config as `databaseURL: "..."`.

### 5. Put the config in the game
You can do either of these:

**Option A: in the file (works for everyone you send the file to).**
Open `index.html` in a text editor and search for `PUT YOUR FIREBASE CONFIG HERE`. Just below it is this line:

```js
const firebaseConfig = null;
```

Replace that **one line** with the block you copied from Firebase:

```js
const firebaseConfig = {
  apiKey: "AIza....",
  authDomain: "doors-with-friends.firebaseapp.com",
  databaseURL: "https://doors-with-friends-default-rtdb.firebaseio.com",
  projectId: "doors-with-friends",
  storageBucket: "doors-with-friends.appspot.com",
  messagingSenderId: "1234567890",
  appId: "1:1234567890:web:abcdef123456"
};
```

> ⚠️ Paste **only** the `const firebaseConfig = { ... };` part. Firebase also shows lines like `import { initializeApp } ...` and `const app = initializeApp(...)`. **Don't** paste those, and don't leave the old `const firebaseConfig = null;` line in as well. If something is off, the multiplayer screen tells you the config block has a typo.

**Option B: in-game (saved only on that computer).**
Open **DOORS → 👥 PLAY WITH FRIENDS**, paste the whole `const firebaseConfig = {...}` block into the **Firebase setup** box, and press **💾 SAVE CONFIG**.

### 6. Play!
1. Everyone opens the game (the same file or link) → **DOORS** → **👥 PLAY WITH FRIENDS** → **🌐 ONLINE**.
2. One person presses **✨ CREATE LOBBY** and shares the 5-letter **code**.
3. Everyone else types the code and presses **JOIN**.
4. Pick a name, a colour, a hat and a skin tone. Your character stands in the hotel lobby with everyone else.
5. Friends press **✔ READY**. The host picks the difficulty and modifiers, then presses **▶ START RUN**.

### Sharing with friends
Everyone needs the same `index.html` with the same Firebase config. The easiest ways:
- **GitHub Pages:** in your repo go to *Settings → Pages*, set the source to the `main` branch, and share the link it gives you.
- Or just send friends the `index.html` file. Double-clicking it works.

### No Firebase yet? Use local test mode
On the multiplayer screen, **🧪 LOCAL TEST** connects tabs **in the same browser on the same computer**. Open the game in two tabs to try multiplayer without any setup. It does not work between different computers; that's what Firebase is for.

---

## 👥 How multiplayer works

- **Same hotel for everyone:** the host makes a seed, and every room, drawer, key and padlock code is generated the same way on each computer.
- **Synced:** doors, unlocking, drawers, gold, keys, chests, hint books, the desk note, fuses, the breaker, Dupe doors, Rush/Ambush, Eyes, chandeliers, and who is hiding where. Only one person fits in each wardrobe, bed or locker.
- **Alex (Seek) chases:** when anyone triggers the chase, **everyone is teleported** into the hallway and the cutscene plays for all. Alex chases whoever is furthest behind. The door only slams once the whole team is through.
- **Mrs. C (Library, Room 50 and Room 100):** when anyone walks in, **everyone is pulled in** and the cutscene plays for all. She hears **every** player, so if your friend is loud, she goes for them. The padlock, clues, fuses and breaker are shared.
- **Personal scares:** Screech, Halt, Giggle, Snares and the Janitor only target the player they find.
- **Dying:** you spectate your teammates (← → to switch). You **respawn next to them** once the team reaches 2 rooms past where you died (not during a chase or a locked library).
- **End of run:** when everyone has escaped or died, you all see the team results. The host can press **🔁 BACK TO LOBBY** for another round.
- **Fair play:** the admin panel (Tab) is turned off in multiplayer, and the hotel doesn't pause.
- Each player keeps their own gold, items, Knobs and pre-run shop purchases.

---

## 🆕 Other new DOORS stuff

### Better room generation
- **The hotel zig-zags:** exit doors are often off to the left or right, so you don't just walk in a straight line any more.
- **Room shapes:** normal rooms, wide halls, long galleries, tiny closets, **maze rooms** (partition walls you have to snake through) and **pillar halls**.
- **11 new themed rooms**, each with its own set pieces and sounds:
  - 🍳 **Hotel Kitchen:** stoves, counters, and a walk-in fridge to hide in
  - 💃 **Grand Ballroom:** a dance floor, a chandelier, and a piano that plays by itself
  - 🌿 **Moonlit Greenhouse:** a fountain and blue moonlight
  - 🚻 **Restrooms:** hide in the stalls
  - 🗄️ **Manager's Office:** glowing monitors and a wall safe
  - 🧺 **Laundry Room**
  - 🔥 **Boiler Room:** steam vents and red light
  - 🖼️ **Art Gallery:** statues that turn to face you when you're not looking
  - 🧸 **Abandoned Playroom:** a rocking horse and a music box
  - 🧹 **Broom, linen and coat closets**
- **New hallways:** Portrait Hall (the eyes follow you), Collapsed Corridor and Service Corridor.
- **Every room and hallway now has a hiding spot.** Before, about half the hallways had none, so Rush there meant certain death.
- Rooms in a run don't repeat the same theme back to back. Lamps have colours (moonlight, boiler glow, office screens).

### More stuff happens
- **Cave-ins:** the ceiling collapses, so dodge the red rings on the floor.
- **Blackouts:** every light in the room dies for a while… and something whispers.
- **Poltergeists:** drawers slam, furniture jolts and books fly. It's harmless, but good luck staying calm.
- **Creepy moments:** shadows crossing the room, bulbs bursting, knocking inside the walls.
- **Never too quiet:** after 3 quiet rooms in a row, something is guaranteed to happen.
- **Mimic gold:** if the gold twitches, it bites.

### Puzzles
- 🔐 **Wall safes:** find the sticky note with the code somewhere in the room to open the safe for gold and rare items.
- ⚡ **Power-out rooms:** the room is dark and the door has no power. Find and pull all 3 levers.

All of this is synced in multiplayer.

### Earlier additions
- **The Janitor:** stay in one room too long and the lights go grey, a bell rings, and he comes to mop you up. Keep moving! (Locked-door rooms give you extra time to find the key.)
- **Lockers:** new hiding spots in the new *Staff Locker Room* and *Gym Storage* rooms.
- **Seeds and daily runs:** type a seed, or press **📅 Daily run**, to play the exact same hotel as your friends and race for the best time. Your seed is shown at the end of each run.
- **Run stats:** the end screen shows doors opened, rushes survived, hides, finds and close calls.
- Fixed a bug that stopped the timer, progress bar, music and FPS counter from loading in DOORS mode.
- Fixed hotels sometimes being generated slightly differently for different players (or browsers) with the same seed.

---

## ❓ Troubleshooting

| Problem | Fix |
| --- | --- |
| "Could not connect to your Firebase Realtime Database" | The `databaseURL` is wrong or missing. Copy it from the top of the Realtime Database **Data** tab. |
| "Permission denied" | Set the **Rules** from step 3 and press **Publish**. |
| "The Firebase config block … has a typo" | You pasted extra lines (like `import …`) or left `const firebaseConfig = null;` in. Keep only one `const firebaseConfig = { … };`. |
| "No lobby with code …" | Check the code. Everyone must use the same Firebase config. |
| A friend shows as 📡 | Their connection dropped or their tab is in the background. They come back when they return. |
| Lag | Firebase is fast, but very far-apart players can see small hiccups. Pick a database location near you. |

Old lobbies stay in the database after everyone leaves. They're tiny, but you can delete the `doorsMP` folder in the Realtime Database **Data** tab at any time.
