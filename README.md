# Gridiron Lab — Flag Football Play Designer

A web-based 5v5 flag football play designer with personal play libraries, team playbooks, and captain-gated team membership.

---

## Files

| File | Purpose |
|------|---------|
| `index.html` | Login / sign-up page |
| `app.html` | Main application (designer + my plays + team hub) |
| `firebase-config.js` | **Your Firebase credentials go here** |
| `firestore.rules` | Firestore security rules to deploy |

---

## Setup

### 1. Firebase project

1. Go to [Firebase Console](https://console.firebase.google.com/) and open your project.
2. Enable **Authentication → Email/Password** sign-in method.
3. Enable **Firestore Database** (start in production mode).

### 2. Add your Firebase config

Open `firebase-config.js` and replace all the placeholder values with the config snippet from:

> Firebase Console → Project Settings → Your apps → Web app → SDK setup and configuration

```js
const FIREBASE_CONFIG = {
  apiKey:            "...",
  authDomain:        "...",
  projectId:         "...",
  storageBucket:     "...",
  messagingSenderId: "...",
  appId:             "..."
};
```

### 3. Deploy Firestore security rules

In your project directory, run:

```bash
firebase deploy --only firestore:rules
```

Or paste the contents of `firestore.rules` directly into:
> Firebase Console → Firestore → Rules

### 4. Deploy to GitHub Pages (or any static host)

**GitHub Pages:**
1. Push all files to your repository.
2. Go to repo Settings → Pages → Source → select your branch and root folder.
3. Your app will be live at `https://yourusername.github.io/yourrepo/`

> **Important:** GitHub Pages serves `index.html` as the default. Navigating directly to `app.html` works fine — it's a direct URL.

---

## How it works

### Play Designer
- Bird's-eye view of the scrimmage area, attacking upward
- Default 5-player lineup: WR-L, Slot, Centre, WR-R, QB (7 yards back)
- **Drag players** to reposition them anywhere on the field
- **Select a player**, then either:
  - Pick a **route preset** (Go, Slant, Curl, Out, In, Corner, Post, Cross, Flat, Screen) — waypoints are placed automatically
  - **Click on the field** to manually add waypoints one by one
- **Drag waypoints** to fine-tune any route
- Choose a **route colour** per player
- **Save** plays to your personal library or to the team playbook
- **Print** any play directly from the designer or from My Plays

### My Plays
- All your personally saved plays in a grid with thumbnail previews
- Click any card to load it back into the designer for editing
- Delete plays you no longer need

### Team Hub

#### Creating a team
- Any user can create a team — they become the **captain**
- A unique 6-character **join code** is generated automatically
- Share the code with your squad

#### Joining a team
- Enter the 6-character code to send a join request
- The captain sees pending requests in real time and can **approve or deny** them
- Approved members immediately gain access to the team playbook

#### Team Playbook
- All team members can view and load team plays
- Any member can save plays to the team playbook from the designer
- The captain can delete any play; members can delete their own

---

## Firestore data structure

```
users/{uid}
  displayName, email, teamId, pendingTeamId, createdAt

plays/{playId}
  ownerId, name, notes, players[], routes{}, thumbnail, updatedAt

teams/{teamId}
  name, code, captainId, memberIds[], createdAt

teams/{teamId}/joinRequests/{uid}
  displayName, email, status (pending|approved|denied), requestedAt

teams/{teamId}/plays/{playId}
  authorId, authorName, name, notes, players[], routes{}, thumbnail, updatedAt
```

---

## Limitations & future ideas

- Players can only be on **one team** at a time
- Captains cannot leave a team (avoids orphaned teams) — consider adding a "transfer captaincy" feature
- No defensive player layer yet — the designer is offence-only for now
- Plays are stored with a base64 thumbnail (~30–60 KB each) — for large playbooks consider Firebase Storage instead
- To add more players or change default positions, edit the `initPlayers()` function in `app.html`
