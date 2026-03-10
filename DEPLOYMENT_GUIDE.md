# LUXE E-Commerce Store
## Complete CI/CD Setup Documentation
### GitHub + Vercel Deployment Guide

---

## 📁 Project Structure

```
luxe-ecommerce-store/
├── .github/
│   └── workflows/
│       └── deploy.yml          ← GitHub Actions CI/CD pipeline
├── src/                        ← Vue.js source files
├── public/                     ← Static assets
├── index.html                  ← Main HTML entry point
├── vercel.json                 ← Vercel deployment config
├── .env.example                ← Environment variables template
├── .env.local                  ← Your local env vars (NOT committed)
├── .gitignore
├── package.json
└── README.md
```

---

## 🚀 STEP 1 — GitHub Repository Setup

### 1.1 Create Repository
1. Go to [github.com](https://github.com) → Click **New Repository**
2. Name it: `luxe-ecommerce-store`
3. Set visibility: **Public** or **Private**
4. Do NOT initialize with README (we'll push existing code)
5. Click **Create Repository**

### 1.2 Initialize & Push Code
```bash
# Navigate to your project folder
cd luxe-ecommerce-store

# Initialize Git
git init

# Add all files
git add .

# First commit
git commit -m "feat: initial project setup"

# Connect to GitHub (replace YOUR_USERNAME)
git remote add origin https://github.com/YOUR_USERNAME/luxe-ecommerce-store.git

# Push to main branch
git push -u origin main
```

### 1.3 Create develop Branch
```bash
# Create and switch to develop branch
git checkout -b develop

# Push develop branch to GitHub
git push -u origin develop
```

---

## ⚡ STEP 2 — Vercel Project Setup

### 2.1 Connect GitHub to Vercel
1. Go to [vercel.com](https://vercel.com) → **Add New Project**
2. Click **Import Git Repository**
3. Select `luxe-ecommerce-store`
4. Framework Preset: **Vue.js**
5. Build Command: `npm run build`
6. Output Directory: `dist`
7. Click **Deploy**

### 2.2 Configure Environments in Vercel
Go to **Project Settings → Environment Variables** and add:

| Variable | Value | Environment |
|---|---|---|
| `VITE_APP_NAME` | `LUXE Store` | All |
| `VITE_APP_URL` | `https://luxe-store.vercel.app` | Production |
| `VITE_API_BASE_URL` | `https://api.luxe-store.com` | Production |
| `VITE_API_BASE_URL` | `https://staging-api.luxe-store.com` | Preview |
| `VITE_STRIPE_PUBLIC_KEY` | `pk_live_...` | Production |
| `VITE_STRIPE_PUBLIC_KEY` | `pk_test_...` | Preview |

### 2.3 Get Your Vercel Token
1. Go to **Vercel Dashboard → Settings → Tokens**
2. Click **Create Token**
3. Name it: `github-actions`
4. Scope: Full Account
5. Copy the token (you'll need it next)

---

## 🔐 STEP 3 — GitHub Secrets Setup

Go to your GitHub repo → **Settings → Secrets and variables → Actions**

Add these secrets:

| Secret Name | Value |
|---|---|
| `VERCEL_TOKEN` | Your Vercel API token |
| `VERCEL_ORG_ID` | Found in Vercel Project Settings |
| `VERCEL_PROJECT_ID` | Found in Vercel Project Settings |

### How to find Vercel IDs:
```bash
# Install Vercel CLI
npm install -g vercel

# Login and link project
vercel login
vercel link

# This creates .vercel/project.json with orgId and projectId
cat .vercel/project.json
```

---

## 🔄 STEP 4 — CI/CD Pipeline Explained

### How the Pipeline Works:

```
Developer pushes code
        │
        ▼
┌───────────────────┐
│  GitHub Actions   │
│  Triggered        │
└─────────┬─────────┘
          │
          ▼
┌───────────────────┐
│  Job 1: Build     │  ← Runs on every push
│  & Lint Check     │  ← Fails fast if code broken
└─────────┬─────────┘
          │
    ┌─────┴──────┐
    │            │
    ▼            ▼
┌────────┐  ┌──────────┐
│develop │  │  main    │
│branch  │  │  branch  │
└───┬────┘  └────┬─────┘
    │             │
    ▼             ▼
┌────────┐  ┌──────────┐
│Preview │  │Production│
│Deploy  │  │Deploy    │
│ URL    │  │ URL      │
└────────┘  └──────────┘
```

### Branch Strategy:

| Branch | Purpose | Deploys To |
|---|---|---|
| `main` | Production-ready code | Production (live site) |
| `develop` | Development/staging | Preview URL |
| `feature/*` | New features | PR preview only |

### Workflow:
```bash
# Start a new feature
git checkout develop
git checkout -b feature/add-cart-page

# Work on your feature...
git add .
git commit -m "feat: add cart page"
git push origin feature/add-cart-page

# Create Pull Request: feature → develop
# → Triggers Preview Deploy for review

# Merge to develop → Auto Preview Deploy
# Merge to main → Auto Production Deploy
```

---

## 🌍 STEP 5 — Custom Domain Setup

### 5.1 Add Domain in Vercel
1. Go to **Project Settings → Domains**
2. Click **Add Domain**
3. Enter: `yourdomain.com`
4. Also add: `www.yourdomain.com`

### 5.2 Configure DNS Records
Go to your domain registrar (GoDaddy, Namecheap, etc.) and add:

| Type | Name | Value |
|---|---|---|
| `A` | `@` | `76.76.21.21` |
| `CNAME` | `www` | `cname.vercel-dns.com` |

### 5.3 Verify Domain
- DNS changes take 24–48 hours to propagate
- Vercel automatically provisions SSL certificate
- Check status in Vercel Dashboard → Domains

---

## 🛠️ STEP 6 — Local Development

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/luxe-ecommerce-store.git
cd luxe-ecommerce-store

# Install dependencies
npm install

# Copy environment variables
cp .env.example .env.local
# Fill in your values in .env.local

# Start development server
npm run dev
# → Runs at http://localhost:5173

# Build for production
npm run build

# Preview production build locally
npm run preview
```

---

## 🐛 STEP 7 — Common Errors & Fixes

### Error: Build Failed — Module not found
```bash
# Fix: Clear cache and reinstall
rm -rf node_modules package-lock.json
npm install
```

### Error: Environment Variables Not Working
```bash
# In Vue 3 + Vite, all env vars must start with VITE_
# ❌ Wrong:  API_KEY=abc123
# ✅ Correct: VITE_API_KEY=abc123
```

### Error: Vercel Deployment Fails — Exit Code 1
1. Check build logs in Vercel Dashboard
2. Run `npm run build` locally first
3. Fix any errors shown in the terminal

### Error: Custom Domain Not Working
1. Verify DNS records are correct
2. Wait 24–48 hours for propagation
3. Check Vercel domain status for errors

### Error: GitHub Actions — VERCEL_TOKEN invalid
1. Regenerate token in Vercel Settings
2. Update the GitHub Secret with new token
3. Re-run the failed workflow

---

## ✅ Deployment Checklist

Before going live, verify:

- [ ] All environment variables added in Vercel
- [ ] `main` branch is protected (require PR reviews)
- [ ] `.env.local` is in `.gitignore`
- [ ] Build passes locally (`npm run build`)
- [ ] GitHub Actions secrets are set
- [ ] Custom domain DNS is configured
- [ ] SSL certificate is active (Vercel auto-handles)
- [ ] Preview deployment tested on `develop`
- [ ] Production deployment verified on `main`

---

## 📞 Support

After delivery, you get **3 days of post-delivery support**.
For any issues, message me directly on Fiverr.

---

*Documentation prepared as part of the Premium CI/CD Setup package.*
