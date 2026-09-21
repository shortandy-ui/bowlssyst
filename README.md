# Bowls Club — Availability, Selection, Rink Booking & Officers

A single self-contained web app for a bowls club: fixture management, player
availability, captain team selection, rink booking with committee approval,
and a club officers noticeboard. No build step, no backend, no database —
it's one HTML file that runs entirely in the browser.

## How it works

- Everything (fixtures, availability, team sheets, bookings, officers,
  passwords, branding) lives **in memory in the browser tab only**. Nothing
  is sent to a server and nothing survives a page reload.
- Use **Admin → Settings → Export data** to download a JSON backup, and
  **Import data** to load it back in on the same device. This is the way
  data carries over between sessions right now.
- Roles are gated by simple passwords (Player / Captain / Admin /
  Committee), set in Admin → Settings. This is not real security — anyone
  who knows a password gets that role's access — it's there to stop
  accidental edits, not a determined bad actor.

## Running it locally

There's nothing to install. Just open `index.html` in a browser, or serve
the folder with any static file server, e.g.:

```bash
npx serve .
# or
python3 -m http.server 8080
```

## Deploying — GitHub + Azure Static Web Apps

This app is a static site, so [Azure Static Web Apps](https://azure.microsoft.com/en-us/products/app-service/static)
(which has a free tier) is a good fit. The repo already includes the
GitHub Actions workflow Azure needs, at
`.github/workflows/azure-static-web-apps.yml`.

**1. Push this folder to a new GitHub repository**

```bash
cd bowls-club-app
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

**2. Create the Azure Static Web App**

- In the [Azure Portal](https://portal.azure.com), create a new resource →
  **Static Web App**.
- Choose the **Free** plan (fine for this app).
- Under **Deployment details**, sign in with GitHub and pick the
  repository and branch (`main`) you just pushed.
- Build details:
  - **Build presets:** Custom
  - **App location:** `/`
  - **Api location:** *(leave blank — there is no API)*
  - **Output location:** *(leave blank)*
- Click **Review + create**, then **Create**.

Azure will commit a workflow file into your repo (or you can keep the one
already included here) and add an `AZURE_STATIC_WEB_APPS_API_TOKEN` secret
to the repository automatically. The first deployment kicks off right
away — you'll get a `https://<random-name>.azurestaticapps.net` URL within
a couple of minutes. Every push to `main` after that redeploys
automatically.

**3. (Optional) Custom domain**

Once it's live, you can attach a custom domain (e.g. `bowls.yourclub.org.uk`)
from the Static Web App's **Custom domains** blade in the Azure Portal.

## Selling / distributing to other clubs

Since data lives only in the browser and there's no shared backend, each
club that buys this needs **their own copy** of `index.html` — either:

- hosted on their own Azure Static Web App (as above), or
- simply opened locally / hosted on any web space they already have.

There is currently **no payment gate or license check** built into the
app — that was deliberately deferred. If you want to add one before
selling copies more widely (e.g. an activation key, or a build step that
strips features from a "trial" copy), that's a separate piece of work.

## Project structure

```
bowls-club-app/
├── index.html                          the entire app
├── staticwebapp.config.json            Azure Static Web Apps routing config
├── .github/workflows/
│   └── azure-static-web-apps.yml       CI/CD: deploy to Azure on push to main
└── README.md
```

## License

© All rights reserved. This code is not licensed for redistribution or
resale by anyone other than the copyright holder.
