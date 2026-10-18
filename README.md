# Enliven website

Static site for [enlivenup.org](https://enlivenup.org) — no build step. Hosted on Netlify; every push to `main` goes live in about a minute.

**Editing the words, photos and videos needs no code.** Board members use the content manager at **https://enlivenup.org/admin/**.

---

## 1. Editing the site (for board members)

1. Go to **enlivenup.org/admin/** and log in with your email and password.
   (If you have never logged in, you need an invitation first — see §2.)
2. Pick the section you want to change:
   - **Contact & settings** — sign-up link, email, phone, footer, sun-ray strength.
   - **Home page** — headline, intro, photos, numbers, mission text, featured quote, recent moments, partners.
   - **About page** — heading, about text, large photo, founding year, **team members**.
   - **Gallery** — photos and videos.
   - **Testimonials page** — featured testimonial, testimonial cards, bottom heading.
3. Change what you need. Every field has a short grey hint under it.
4. Press **Publish → Publish now** (top right). The site updates within a minute or two. Refresh enlivenup.org to see it.

Each publish is saved as a change in GitHub with your name on it, so nothing is ever lost (see §4 for undoing).

### Common tasks

**Change the sign-up link** — Contact & settings → *Sign-up form link* → paste the new Google Form URL → Publish. Every "Sign up" button on the site uses it.

**Add a team member** — About page → *Team members* → **Add team members** (bottom of the list). Fill in name, school/role, bio, and upload a photo (square photos look best; they are shown as a 160 × 160 rounded square). Drag the handle on the left of a card to reorder; the trash icon removes someone. *Photo focus point* is optional — `50% 20%` keeps the top of a photo in view, useful for faces.

**Add a photo to the gallery** — Gallery → *Photos* → **Add photos** → *Choose an image* → **Upload** a JPG → add a one-line description → Publish. New items go to the end; drag to reorder. Please resize very large photos before uploading (under ~1 MB each keeps the page fast).

**Add a video** — Gallery → *Videos* → **Add videos** → *Video file* → upload an **MP4**. Keep clips short (a few seconds to a minute; under ~25 MB). *Cover image* and *Length in seconds* are optional: if left blank the site shows the video's first frame and reads the length from the file. Add a description and Publish. Videos appear in the gallery after every two photos, in the order listed.

**Add a testimonial** — Testimonials page → *Testimonial cards* → **Add testimonial cards** → label (e.g. the community name), the quote (no quotation marks needed), attribution → Publish. To change the large featured testimonial, edit *Featured testimonial* on the same page.

**Home-page photos** — Home page → *Hero photos* (three large photos under the headline; the middle one is shown taller) and *Recent moments* (the four small ones).

**Descriptions "for screen readers"** are required on every photo and video. Many visitors use screen readers or have low vision; one plain sentence about what is in the picture is enough.

Tips:
- The headline word "intergenerational" is allowed to break as *inter‑/generational* on phones automatically. You can retype it freely.
- In the longer text fields, leave an **empty line** between paragraphs.
- The CMS does not show a preview. Publish, then check the live site; undoing is easy (§4).

---

## 2. Inviting a new editor (Netlify admin)

Registration is invite-only. An admin of the Netlify account invites people:

1. Log in to **app.netlify.com** → open the **enliven** site.
2. Left menu: **Integrations → Identity** (older dashboards: the **Identity** tab) → **Invite users**.
3. Enter their email and send. They get an email with an **Accept the invite** link.
4. The link opens enlivenup.org, which hands them to `/admin/` where they set a password. After that they log in at **enlivenup.org/admin/**.

To remove someone: Identity → click the user → **Delete user**. To let someone reset a forgotten password: on the `/admin/` login screen they choose **Forgot password?**; the recovery email works the same way as the invite.

---

## 3. Netlify settings checklist (one-time, done in the dashboard)

These are **not** in code and must be switched on once for the CMS to work:

- [ ] **Identity** — Site → Integrations → Identity → **Enable Identity**.
- [ ] **Registration: Invite only** — Identity → Configuration → Registration preferences → **Invite only**.
- [ ] **Git Gateway** — Identity → Services → **Enable Git Gateway** (this lets logged-in editors write to the GitHub repo without a GitHub account). Roles can be left empty.
- [ ] (Optional) **External providers** — leave off; email + password is simplest for volunteers.
- [ ] **Build settings** — Site configuration → Build & deploy: **Build command** empty, **Publish directory** `/` (repo root), branch `main`.
- [ ] Invite the first editors (§2).

If editors see "Error: Failed to load settings from /.netlify/identity" Identity is not enabled. If login works but saving fails with a Git Gateway error, Git Gateway is off or its GitHub token expired — re-enable it in Identity → Services.

---

## 4. Undoing a bad edit

Every publish is a commit on GitHub. To roll back:

**Option A — GitHub (undo one change):** open https://github.com/ajayw36/enliven-website/commits/main, find the commit (CMS commits are titled "Update content: … (via CMS by Name)"), open it, and press **Revert** → **Create pull request** → **Merge**. Netlify redeploys automatically. Or just fix the field again in the CMS and publish.

**Option B — Netlify (roll back the whole site instantly):** Netlify → Deploys → pick an earlier successful deploy → **Publish deploy**. This restores the site immediately but does **not** change the files on GitHub — the next CMS publish will bring the bad edit back, so also fix it with Option A or in the CMS.

---

## 5. For developers

```
index.html        the whole site: template + a small logic class at the bottom
support.js        the rendering runtime (don't edit)
content/*.json    ALL editable content — site, home, about, gallery, testimonials
admin/            Decap CMS (index.html loads it; config.yml defines the fields)
photos/ videos/   media (videos/ also holds optional .jpg poster frames)
assets/           logo
design/           the original design exploration; context only, not served
image-slot.js     legacy drag-and-drop photo slot; no longer loaded by index.html
```

- `index.html` fetches the five JSON files on load and exposes them to the template (`{{ home.heroSubtitle }}`, `<sc-for list="{{ team }}">`, …). The logic class only shapes data — paragraph splitting, the `+` in the stats, gallery interleaving (one video after every two photos, leftover videos appended), optional video posters/durations. Styling is inline and unchanged from the original design.
- Field names in `admin/config.yml` must match the JSON keys. If you add a field: add it to the JSON, to `config.yml`, and read it in the template.
- Media paths are root-relative (`/photos/x.jpg`) because Decap writes them that way.
- Routing is by hash: `#home` `#about` `#gallery` `#testimonials`; `#team` opens About and scrolls to the team.

**Run locally**
```sh
npx serve .            # site at http://localhost:3000
npx decap-server       # in a second terminal: lets /admin edit the local files, no login needed
```
Open http://localhost:3000/admin/ and click *Login*. `local_backend: true` in `config.yml` is what enables this; it is ignored on the live site.

**Poster frame for a video (optional)**
```sh
ffmpeg -i videos/clip-15.mp4 -ss 00:00:00.5 -frames:v 1 -q:v 3 videos/clip-15.jpg
```

**Design planning** — `design/Enliven Website Directions.html` has the three original directions (1a/1b/1c) and the chosen blend (2a), desktop + mobile.
