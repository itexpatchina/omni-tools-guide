---
title: "Deploying OmniTools on Cloudflare Pages: Subdomain Setup Guide"
date: 2026-10-04
draft: false
tags: ["Cloudflare", "OmniTools", "Homelab", "Vite", "React", "DevOps"]
summary: "A step-by-step guide to hosting OmniTools on Cloudflare Pages with a custom subdomain omni.itexpatchina.com."
---

# Deploying OmniTools on Cloudflare Pages: Subdomain Setup Guide

A complete, production-ready guide to deploying **OmniTools** (`iib0011/omni-tools`) — a 100% privacy-first, client-side open-source utility suite — onto **Cloudflare Pages** using a custom subdomain (`https://omni.itexpatchina.com/`).

---

## 📌 Overview & Architecture

**[OmniTools](https://github.com/iib0011/omni-tools)** is a modern, web-based Swiss Army knife offering over 80+ daily utilities for developers and digital nomads (image compression, PDF editing, text formatting, date calculators, JSON tools, and WASM-powered video processing).

Because all data processing happens **entirely in the user's browser (client-side)** with zero database or backend server requirements, OmniTools is a perfect candidate for global edge hosting on **Cloudflare Pages**.

### 🛠️ Tech Stack & Build Characteristics
* **Framework:** React + TypeScript + Material UI + Vite
* **Execution Model:** 100% Client-Side Single Page Application (SPA) with WebAssembly
* **Build Artifacts:** Static web assets output to the `dist/` directory
* **Backend Requirement:** None (Zero database, zero API server)

---

## 🎯 Why Subdomain Routing (`omni.itexpatchina.com`)

Deploying OmniTools on a custom subdomain provides the cleanest and most efficient infrastructure:

* 🟢 **Zero Maintenance:** Uses Cloudflare Pages' native custom domain routing without needing complex Worker reverse proxies.
* ⚡ **Default Vite Compatibility:** Uses standard base path (`base: '/'`), avoiding asset loading issues.
* 🔒 **Automated SSL & CDN:** Cloudflare automatically issues and renews universal SSL certificates for `omni.itexpatchina.com`.
* 🌐 **Global Edge Caching:** Static assets are cached across Cloudflare's 300+ global edge locations for sub-second load times worldwide.

---

## 🚀 Step-by-Step Deployment Guide

### **Step 1: Fork or Clone the OmniTools Repository**
Fork the official repository (`iib0011/omni-tools`) to your GitHub account or clone it locally:

```bash
git clone https://github.com/iib0011/omni-tools.git
cd omni-tools
```

---

### **Step 2: Add SPA Fallback for Client-Side Routing**
Because OmniTools is a Single Page Application (SPA) using client-side routing, directly refreshing or navigating to deep URLs (e.g., `/tool/image/convert`) will return a 404 error unless Cloudflare Pages knows to redirect all requests to `index.html`.

Add a `_redirects` file to the `public/` folder:

```bash
# Create public/_redirects rule for Cloudflare Pages
echo "/* /index.html 200" > public/_redirects
```

Commit this file to your repository:

```bash
git add public/_redirects
git commit -m "feat: add Cloudflare Pages SPA redirect rule"
git push
```

---

### **Step 3: Connect & Deploy on Cloudflare Pages**

1. Log in to the **[Cloudflare Dashboard](https://dash.cloudflare.com/)**.
2. Go to **Workers & Pages** → **Create Application** → **Pages** → **Connect to Git**.
3. Select your `omni-tools` repository and click **Begin setup**.
4. Configure the build parameters:
   * **Project Name:** `omni-tools`
   * **Production Branch:** `main`
   * **Framework Preset:** `Vite` (or `None`)
   * **Build Command:** `npm run build`
   * **Build Output Directory:** `dist`
5. Click **Save and Deploy**.

Cloudflare will pull the source code, install node dependencies, compile the React build, and allocate a temporary preview URL (`omni-tools.pages.dev`).

---

### **Step 4: Map Custom Subdomain (`omni.itexpatchina.com`)**

1. Inside your Cloudflare Pages project, select the **Custom domains** tab.
2. Click **Set up a custom domain**.
3. Type **`omni.itexpatchina.com`** and click **Continue**.
4. Click **Activate domain**.

Cloudflare will automatically manage the DNS CNAME record (`omni.itexpatchina.com` → `omni-tools.pages.dev`) and provision an SSL certificate within 1–2 minutes.

---

## 🔐 Verification & Testing

1. **Subdomain Verification:** Open `https://omni.itexpatchina.com` in your browser.
2. **SPA Deep-Link Refresh Test:** Navigate to any inner tool page (e.g., `https://omni.itexpatchina.com/tool/image/convert`) and press `F5` / `Ctrl + R`. The page should reload cleanly without a 404 error.
3. **CI/CD Pipeline:** Any future `git push` to your GitHub repository will automatically trigger a clean build and deploy instantly to Cloudflare Pages.
