# Barney's BinDaRoids - Setup Guide

## 1. Publish to GitHub Pages (free, anyone can play)

### One-time setup:

1. Make sure the repo is pushed to GitHub:
   ```bash
   git add index.html SETUP.md
   git commit -m "Add BinDaRoids game with leaderboard"
   git push origin main
   ```

2. Go to your GitHub repo in a browser:
   `https://github.com/YOUR_USERNAME/Barneys-BinDaRoids`

3. Click **Settings** (top tab)

4. In the left sidebar click **Pages**

5. Under "Source" choose:
   - **Deploy from a branch**
   - Branch: **main**
   - Folder: **/ (root)**

6. Click **Save**

7. After 1-2 minutes your game is live at:
   `https://YOUR_USERNAME.github.io/Barneys-BinDaRoids/`

That's it! Share that URL with anyone - works on phone, tablet, laptop, desktop.

---

## 2. Set Up the Global Leaderboard with Supabase (free tier)

The game works without this (uses local hi-score only), but with Supabase
anyone in the world can compete on the same leaderboard.

### Step 1 - Create a Supabase project

1. Go to [supabase.com](https://supabase.com) and sign up (free)
2. Click **New Project**
3. Give it a name (e.g. `bindaroids`), choose a region close to you, set a password
4. Wait ~2 minutes for it to spin up

### Step 2 - Create the scores table

1. In your Supabase project, click **SQL Editor** in the left sidebar
2. Paste and run this SQL:

```sql
CREATE TABLE scores (
  id         bigint generated always as identity primary key,
  name       text    not null,
  score      integer not null,
  level      integer not null,
  created_at timestamptz default now()
);

ALTER TABLE scores ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Anyone can read scores"
  ON scores FOR SELECT USING (true);

CREATE POLICY "Anyone can submit a score"
  ON scores FOR INSERT WITH CHECK (true);
```

3. Click **Run**

### Step 3 - Get your API credentials

1. In your Supabase project, click the **Settings** gear icon
2. Click **API** in the left menu
3. Copy two values:
   - **Project URL** (looks like `https://xxxxxxxxxxx.supabase.co`)
   - **anon public** key (long string starting with `eyJ...`)

### Step 4 - Add credentials to the game

Open `index.html` and find these two lines near the top of the `<script>` section:

```javascript
const SUPABASE_URL      = 'YOUR_SUPABASE_URL';
const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY';
```

Replace the placeholder strings with your actual values:

```javascript
const SUPABASE_URL      = 'https://xxxxxxxxxxx.supabase.co';
const SUPABASE_ANON_KEY = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...';
```

Save the file, commit and push:

```bash
git add index.html
git commit -m "Add Supabase leaderboard credentials"
git push origin main
```

GitHub Pages will update automatically within a minute.

### Step 5 - View scores in Supabase

In Supabase click **Table Editor** then **scores** to see all submitted scores.
You can delete any offensive names from there directly.

---

## Security note

The `anon` key is safe to expose publicly - it only has the permissions
defined by your Row Level Security policies (read + insert only).
Never paste your `service_role` key into the game file.
