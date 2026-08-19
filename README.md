# Caspa's Luxury Salon — Next.js Front End (Vercel side)

This is the **front-end half** of the hybrid setup: all customer-facing
pages (Home, About, Services, Barber, Salon, Spa, Offers, Booking,
Contact), built with Next.js (App Router) for deployment on Vercel.
The booking API and staff admin dashboard live separately on PHP/MySQL
hosting — see the `php-backend` folder's own README for that half.

```
Vercel (this app)  ──fetch()──>  cPanel (PHP backend)
  caspas.vercel.app                  datardius.co.ke/backend/*.php
```

## 1. Deploy the PHP backend first

Before deploying this app, make sure `php-backend/` (from the other
delivered package) is already live on your cPanel hosting with its
database configured — see that folder's README. You'll need its URL
for step 3 below.

## 2. Push this project to GitHub

Vercel deploys from a Git repo:
```bash
cd caspas-next
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/yourusername/caspas-next.git
git push -u origin main
```

## 3. Import into Vercel

1. Go to [vercel.com/new](https://vercel.com/new) and import the repo.
2. Vercel auto-detects Next.js — no build config changes needed.
3. Before deploying, add an **Environment Variable**:
   - `NEXT_PUBLIC_API_URL` = `https://datardius.co.ke` (your PHP backend's base URL, no trailing slash)
4. Deploy.

## 4. Update the backend's allowed origins

Once deployed, note your Vercel URL (e.g. `https://caspas.vercel.app`).
Go back to `php-backend/backend/config.php` on your cPanel host and add
that exact URL to `ALLOWED_ORIGINS`, then re-upload/save that file.
Without this step, the browser will block booking requests with a CORS
error since the two are now on different domains.

## 5. Test end-to-end

Visit your Vercel URL, go to **Book an Appointment**, and submit a test
booking. Then check `https://datardius.co.ke/admin/dashboard.php` (log
in with the staff account you created) — the booking should appear.

## Local development

```bash
npm install
cp .env.example .env.local
# edit .env.local with your backend URL (or a local PHP dev server)
npm run dev
```

Runs at `http://localhost:3000`. If testing against your live PHP
backend, `http://localhost:3000` is already included in the example
`ALLOWED_ORIGINS` in `backend/config.php` — no changes needed on that
side for local dev.

## Project structure

```
app/
├── layout.tsx          Root layout — providers, header, footer, modal, toast
├── globals.css         All site styling (ported from the original design)
├── page.tsx             Home
├── about/page.tsx
├── services/page.tsx
├── barber/page.tsx
├── salon/page.tsx
├── spa/page.tsx
├── offers/page.tsx
├── booking/page.tsx
└── contact/page.tsx
components/
├── Header.tsx           Nav with mobile hamburger menu
├── Footer.tsx
├── Cursor.tsx            Custom cursor (desktop only)
├── RevealOnScroll.tsx    Scroll-reveal animations, re-runs per route
├── Toast.tsx
├── QuickBookModal.tsx    "Quick Book" popup, callable from any page
├── BookButton.tsx        Button that opens the quick-book modal
├── HeroSlideshow.tsx     Homepage hero image carousel
├── SalonTabs.tsx         Salon page category tabs
└── BookingWizard.tsx     4-step booking flow (booking page)
lib/
├── config.ts             Backend API URL (from env var)
├── SiteContext.tsx        Shared toast + modal state (React Context)
└── validation.ts          Phone/email validation matching backend rules
```

## Adding your real photos

- Homepage hero: edit `HERO_IMAGES` in `components/HeroSlideshow.tsx`
- Placeholder blocks throughout the pages (search for "Your photo here")
- Once you have real images, consider swapping `<img>`/background-image
  usage for `next/image` for automatic optimization — not done here to
  keep the design 1:1 with the original CSS-driven layout.

## Adding your real map

In `app/contact/page.tsx`, replace the `.map-placeholder` div with a
Google Maps `<iframe>` embed — see the comment right below it in that
file for the exact snippet.
