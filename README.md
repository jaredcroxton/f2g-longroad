# Fuel 2 Go — website

The Fuel 2 Go marketing site. One page, no build step, no database, no API keys.
Everything it needs is in this repository.

Live reference copy: https://f2g-longroad.vercel.app

---

## Run it on your computer

You need nothing installed except Python, which comes with macOS and most Linux systems.

```bash
git clone https://github.com/jaredcroxton/f2g-longroad.git
cd f2g-longroad
python3 -m http.server 5036
```

Then open **http://localhost:5036** in your browser.

Do not open `index.html` by double-clicking it. The scroll film loads hundreds of
image files, and browsers block that when a page is opened straight off the disk.
It has to be served over `http://`, which is all the command above does.

To stop the server, press `Ctrl + C` in the terminal.

---

## Put it live on Vercel

Deploying is free and takes about a minute. Two ways:

**The easy way, through the website**

1. Sign up at https://vercel.com and connect your GitHub account.
2. Click **Add New → Project**, pick this repository, and press **Deploy**.
3. Vercel gives you a live address. Every push to `main` redeploys automatically.

**From the terminal**

```bash
npm i -g vercel
vercel deploy --prod
```

There is no framework to select and no environment variables to set. Vercel serves
the files exactly as they sit in the repository.

### Pointing your own domain at it

In the Vercel dashboard, open the project, go to **Settings → Domains**, and add
your domain. Vercel shows you the DNS records to add at your registrar.

---

## After you go live, change these three addresses

The share-link settings still point at the handover address. Open `index.html`,
search for `f2g-longroad.vercel.app`, and replace all of them with your real domain.
There are four in the head of the file (`canonical`, `og:url`, `og:image`,
`twitter:image`). Do the same in `robots.txt` and `sitemap.xml`.

If you skip this, the site still works perfectly. Only the preview card that appears
when someone shares the link would pull from the old address.

---

## What is in here

```
index.html            The entire website: markup, styles and scripts in one file
vercel.json           Cache rules for hosting
robots.txt            Search engine instructions
sitemap.xml           Search engine page list
assets/
  film2/              534 frames of the scroll film, played as you scroll
  fyrex.mp4           The additive explainer video
  xray-*.png / .jpg   The cutaway diagram of the truck and its glow layers
  *.pdf               Account application and terms and conditions
  logo*.png           Brand marks
  og.jpg              The image shown when the link is shared
```

### The scroll film

The hero is not a video. It is 534 still images drawn onto a canvas, advancing as
you scroll, which is what makes it scrub smoothly in both directions. Two settings
near the bottom of `index.html` control it:

- `FRAME_COUNT` must equal the number of files in `assets/film2/`. It is 534.
  If you add or remove frames, change this number to match or the film will
  stop early or stall at the end.
- `FRAME_VER` is a cache-busting number. If you replace frames and the old ones
  still show in the browser, increase it by one.

### The enquiry form

The form validates what the visitor types, then opens their own email program with
a pre-filled message to **admin@fuel2go.com.au**. It also shows the same text in a
box they can copy, for anyone without email set up on their device.

Nothing is sent to a server, so there is no backend to run, no API key, and no
monthly cost. To change the destination address, search `index.html` for
`admin@fuel2go.com.au` and replace it. It appears three times.

If you later want enquiries to arrive in an inbox or CRM automatically rather than
through the visitor's email program, that needs a form service added. It is a small
change, but it is not in here today.

---

## Editing the site

`index.html` is one self-contained file, which is unusual but deliberate: there is
nothing to compile, and you can change the site with any text editor.

The colours come from the official logo artwork and are set once near the top of
the file:

| Colour | Value | Used for |
|---|---|---|
| Navy | `#030D30` | Backgrounds, headings |
| Keyline grey | `#A7A9AC` | Rules, secondary text |
| Chevron grey | `#6D6E71` | The logo chevrons |
| Gold | `#E8A33D` | Accent, buttons, the fuel glow |

After any edit, refresh the page you have open at `localhost:5036`. There is no
build step, so changes show immediately.

---

## Things worth knowing

- **Company details.** The site names Fuel 2 Go Pty Ltd, ACN 683 689 787.
- **Claims about the additive.** The site deliberately avoids publishing specific
  fuel-saving percentages or carbon credentials. The supporting documents for those
  claims belong to a separate company, so they cannot be published in Fuel 2 Go's
  name without its own substantiation. Check with the business before adding any
  percentage figure or emissions claim.
- **Account application.** It is a downloadable PDF on purpose, not an online form,
  because the signed paper version governs the agreement.
