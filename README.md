# ⚡ 10K Mastery Tracker

Track your journey to 10,000 hours of deliberate practice. Syncs across all your devices via Supabase.

## Setup (5 minutes)

### 1. Create Supabase project

1. Go to [supabase.com](https://supabase.com) and click **Start your project**
2. Create an account (GitHub OAuth is easiest)
3. Click **New project**
4. Name it `10k-tracker` (or whatever)
5. Set a database password (save it somewhere safe)
6. Choose a region close to you
7. Wait ~2 minutes for the project to spin up

### 2. Get your API keys

1. In your Supabase project, go to **Project Settings → API**
2. Copy the **Project URL** and the **anon public key**
3. Open `index.html` in a text editor
4. Find these lines near the top of the script:

```js
const SUPABASE_URL = '';    // ← paste your URL here
const SUPABASE_ANON_KEY = ''; // ← paste your anon key here
```

5. Paste your values between the quotes

### 3. Create the database table

1. In your Supabase project, go to **SQL Editor**
2. Click **New query**
3. Paste this SQL and click **Run**:

```sql
-- Create the entries table
CREATE TABLE entries (
  id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id uuid REFERENCES auth.users NOT NULL,
  skill text NOT NULL,
  sub_skill text,
  hours numeric(5,2) NOT NULL,
  date date NOT NULL,
  notes text,
  created_at timestamptz DEFAULT now()
);

-- Add indexes for performance
CREATE INDEX entries_user_date ON entries(user_id, date DESC);
CREATE INDEX entries_user_skill ON entries(user_id, skill);

-- Enable Row Level Security
ALTER TABLE entries ENABLE ROW LEVEL SECURITY;

-- Users can only see their own entries
CREATE POLICY "Users can CRUD their own entries"
  ON entries FOR ALL
  USING (auth.uid() = user_id)
  WITH CHECK (auth.uid() = user_id);
```

### 4. Enable GitHub OAuth (optional, for easy login)

1. In Supabase, go to **Authentication → Providers**
2. Click **GitHub** and toggle it on
3. Follow the instructions to create a GitHub OAuth app
4. Set the callback URL to: `https://[YOUR-PROJECT-REF].supabase.co/auth/v1/callback`

### 5. Open it

Serve locally:
```bash
python3 -m http.server 8767
```
Then visit **http://localhost:8767**

## Features

- Log practice sessions (skill, sub-skill, hours, date, notes)
- Real-time progress bar toward 10,000 hours
- Skill breakdown with bar chart
- Streak tracking (consecutive days)
- Milestone celebrations (1h, 10h, 50h, 100h, 500h, 1000h, 5000h, 10000h)
- Filter entries by skill
- Export/Import JSON backup
- Syncs across all devices via Supabase

## Deploy

Push this folder to a GitHub repo and enable GitHub Pages. Your data will follow you everywhere.
