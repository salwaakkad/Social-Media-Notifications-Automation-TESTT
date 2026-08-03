# Social Media → Discord Auto-Poster

Whenever your team posts on **YouTube, Instagram, TikTok, or LinkedIn**, this
automatically sends a nicely formatted message with the link + a custom
caption into your Discord channel. Runs forever, checks every 15 minutes,
**100% free**, no server for you to babysit.

Follow every step below in order. Don't skip any. Each one is small on
purpose — just do them one at a time.

---

## What you need before starting

- [ ] A Discord server where you're an admin (or have "Manage Webhooks" permission)
- [ ] A free GitHub account (sign up at github.com if you don't have one)
- [ ] The usernames/handles of your company's YouTube, Instagram, TikTok, and LinkedIn accounts

That's it. No credit card, no paid tools, anywhere.

---

## PART 1 — Create the Discord Webhook

This is the "door" that lets our script send messages into your channel.

1. Open Discord.
2. Go to the server and **channel** where you want the posts to appear.
3. Click the **gear icon** next to the channel name (or right-click the channel → **Edit Channel**).
4. On the left side menu, click **Integrations**.
5. Click **Webhooks**.
6. Click **New Webhook**.
7. Click on the new webhook to open its settings.
8. (Optional) Rename it to something like `Social Media Bot`.
9. Click **Copy Webhook URL**.
10. Paste that URL into a Notes app or text file for now — you'll need it again in Part 4. **Keep it private**, anyone with this URL can post into that channel.
11. Click **Save Changes**.

### Test the webhook actually works
12. Open a terminal / command prompt on your computer.
13. Paste this in, replacing `YOUR_WEBHOOK_URL` with the URL you copied:
```bash
curl -H "Content-Type: application/json" -d "{\"content\": \"Webhook test - it works!\"}" YOUR_WEBHOOK_URL
```
14. Go check your Discord channel. You should see the message "Webhook test - it works!" appear.
15. If you see it → Part 1 is done. If not, redo steps 3–11.

---

## PART 2 — Find your YouTube Channel ID

YouTube gives every channel a free official RSS feed — this is the most
reliable of all 4 platforms, so we do this one first.

1. Go to your channel's YouTube page.
2. Click **your channel's profile picture** in the top right, or go directly to your channel's "About" page.
3. On the channel page, click the **"..." (more) button** or go to **Settings → Advanced settings** — you're looking for the field labeled **"Channel ID"**.
   - Easiest way: go to `https://www.youtube.com/account_advanced` while logged into the channel's account. Your **Channel ID** is shown right there.
4. Copy the Channel ID — it looks like `UCxxxxxxxxxxxxxxxxxxxxxx`.
5. Save it in your notes file next to the webhook URL.

You now have everything needed for YouTube. No further YouTube-specific setup — YouTube's feed is official and doesn't need any extra tool.

---

## PART 3 — Get RSS feeds for Instagram, TikTok, LinkedIn (via RSSHub)

YouTube gives us a free feed directly. Instagram, TikTok, and LinkedIn don't
offer that for free, so we use a free open-source tool called **RSSHub**
that turns their public pages into a feed our script can read.

### 3A — Instagram
1. Go to `https://rsshub.app/instagram/user/YOUR_INSTAGRAM_USERNAME` in your browser — replace `YOUR_INSTAGRAM_USERNAME` with your real handle (no `@`).
2. Wait a few seconds for it to load.
3. **If you see XML/code text with your recent posts** → it works, copy that exact URL, save it in your notes.
4. **If you see an error or blank page** → don't panic, this happens sometimes since Instagram actively blocks scrapers. Skip it for now — see "If Instagram or LinkedIn won't work" near the bottom of this README. You can add Instagram later without redoing anything else.

### 3B — TikTok
1. Go to `https://rsshub.app/tiktok/user/YOUR_TIKTOK_USERNAME` — replace with your real handle (no `@`).
2. Same check as above: XML with posts = works, copy & save the URL.
3. TikTok is normally the most reliable of these three.

### 3C — LinkedIn
1. Go to `https://rsshub.app/linkedin/company/YOUR_LINKEDIN_COMPANY_SLUG` — the slug is the part of your LinkedIn company URL after `/company/`, e.g. for `linkedin.com/company/microsoft` it's `microsoft`.
2. Same check as above.
3. LinkedIn is the least reliable of the four to scrape for free — if it fails, that's expected and normal, see the fallback note at the bottom.

---

## PART 4 — Edit `config.json`

Open the `config.json` file in this folder with any text editor (Notepad, VS Code, TextEdit — anything works).

You'll see 4 blocks, one per platform, each between `{` and `}`. Do this for each one:

### For YouTube
1. Find the block that starts with `"name": "YouTube - CompanyChannel"`.
2. Find the line `"feed_url": "https://www.youtube.com/feeds/videos.xml?channel_id=YOUR_YOUTUBE_CHANNEL_ID"`.
3. Replace `YOUR_YOUTUBE_CHANNEL_ID` with the Channel ID you saved in Part 2. Keep everything else in that line exactly the same, including the quote marks.

### For Instagram, TikTok, LinkedIn
4. Find each `"route"` line, e.g. `"route": "/instagram/user/YOUR_IG_USERNAME"`.
5. Replace only the placeholder part (e.g. `YOUR_IG_USERNAME`) with your real username. Keep the `/instagram/user/` part as-is.
6. Do the same for the TikTok and LinkedIn blocks.
7. **If a platform failed the test in Part 3** (showed an error), leave that block as-is for now — just don't worry about it, it simply won't post anything until you fix it later. It won't break the others.

### Optional: customize the caption text
8. Each block has a `"caption_template"` line — this is the exact message that gets sent to Discord.
9. You can freely rewrite the text. Keep these placeholders somewhere in the text if you want them (you can delete the ones you don't want):
   - `{account}` → the name you gave that feed
   - `{link}` → the link to the actual post
   - `{summary}` → a short auto-generated snippet of the post text
   - `{title}` → the raw post title
10. Save the file.

### Double-check you didn't break the file
11. Go to `https://jsonlint.com`, paste the entire contents of `config.json`, click **Validate JSON**.
12. It should say "Valid JSON". If it shows an error, it's almost always a missing or extra comma — fix and re-check.

---

## PART 5 — Upload this whole folder to GitHub

1. Log into github.com.
2. Click the **+** icon top-right → **New repository**.
3. Name it anything, e.g. `social-to-discord`.
4. Set it to **Private** (still 100% free).
5. Click **Create repository**.
6. On the new empty repo page, click **uploading an existing file**.
7. Drag the **entire contents** of this folder (all files, including the hidden `.github` folder) into the upload box.
   - If your browser hides the `.github` folder from drag-and-drop, instead install **GitHub Desktop** (free, github.com/apps/desktop), open it, choose **Add existing repository**, point it at this folder, and click **Publish repository** — this uploads everything including hidden folders correctly.
8. Click **Commit changes** (or **Publish repository** if using GitHub Desktop).

---

## PART 6 — Add your webhook as a secret

We never put the actual webhook URL inside the code — it's kept as a private "secret" so it's never publicly visible in your repo.

1. In your new GitHub repo, click **Settings** (top menu of the repo, not your account settings).
2. On the left sidebar, click **Secrets and variables** → **Actions**.
3. Click the green **New repository secret** button.
4. In **Name**, type exactly: `DISCORD_WEBHOOK_URL`
5. In **Secret**, paste the webhook URL you saved in Part 1.
6. Click **Add secret**.

---

## PART 7 — Turn it on and test it

1. In your repo, click the **Actions** tab at the top.
2. You should see a workflow called **"Social Media -> Discord"** on the left. Click it.
3. Click the **Run workflow** dropdown button on the right → **Run workflow** (green button).
4. Wait about 20–30 seconds, then refresh the page.
5. Click on the run that just appeared (it'll have a yellow dot → green checkmark once done).
6. Click **post** to expand the log and see what happened — you'll see lines like `[check] YouTube...` and `-> posted: ...` for anything new it found.
7. Go check your Discord channel — if you already have posts on your accounts, you should see them appear there now (it treats existing posts as "new" the very first time it runs).

**It's now fully live.** From this point on, it checks all 4 platforms every
15 minutes automatically, forever, with nothing more for you to do.

---

## How to know it's really working long-term

- Every 15 minutes, GitHub runs it automatically — you don't need to keep your computer on, this runs on GitHub's servers.
- Post something new on any connected platform, wait 15 minutes, check Discord — it should appear.
- To see the history of every automatic run: repo → **Actions** tab, it lists every run with timestamps.

---

## If Instagram or LinkedIn won't work

This is common and not something you did wrong — these two platforms
actively try to block free scraping. If a feed test in Part 3 failed:

- **Nothing else breaks.** YouTube, TikTok, and any working feeds keep posting normally.
- **Easiest fix:** wait a day and retest the same URL from Part 3 — RSSHub's public server issues are often temporary.
- **Manual fallback for now:** whoever posts to that platform just pastes the link into the Discord channel by hand. Takes 10 seconds, and everything else stays fully automatic.
- **Permanent fix (more setup, still free):** self-host RSSHub on a free server (e.g. Oracle Cloud's "Always Free" tier) so it's not sharing the public rate limit and can use a login session for Instagram/LinkedIn. This is an advanced optional upgrade — ask if you want the steps for this later.

---

## Changing how often it checks

1. Open `.github/workflows/social-to-discord.yml`.
2. Find this line:
   ```
   - cron: "*/15 * * * *"
   ```
3. Replace `*/15` with how often you want, in minutes (must divide evenly into 60), e.g. `*/5` for every 5 minutes, `*/30` for every 30 minutes.
4. Save, and re-upload just this file to GitHub the same way as Part 5.

---

## Adding a second account on the same platform

E.g. a second Instagram page.

1. Open `config.json`.
2. Copy an entire `{ ... }` block for that platform.
3. Paste it right after the original block's closing `}`, with a comma between them.
4. Give it a different, unique `"name"`.
5. Change the `"route"` (or `"feed_url"` for YouTube) to the new account.
6. Save, validate on jsonlint.com, re-upload to GitHub.

---

## Files in this package

- `README.md` — this guide
- `config.json` — your accounts + caption templates (edit this)
- `post_to_discord.py` — the script that does the work (no need to edit this)
- `state.json` — auto-managed, tracks what's already been posted so nothing gets sent twice
- `requirements.txt` — Python dependencies (installed automatically by GitHub)
- `.github/workflows/social-to-discord.yml` — the free scheduler that runs everything every 15 minutes
