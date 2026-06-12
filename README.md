# Laravel 12 + React (Vite) Deployment Guide (GoDaddy Shared Hosting)

## Project Stack

* Laravel 12
* React
* Inertia.js
* Vite
* GoDaddy Shared Hosting

---

# Manual Deployment

## Step 1: Install Production Dependencies

Project root me:

```bash
composer install --no-dev
npm install
npm run build
```

Verify:

```text
vendor/
public/build/
```

---

## Step 2: Configure Environment

`.env`

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://yourdomain.com
```

Database credentials update karein.

---

## Step 3: Create ZIP

ZIP me include hona chahiye:

```text
app/
bootstrap/
config/
database/
public/
resources/
routes/
storage/
vendor/
artisan
composer.json
composer.lock
.env
```

---

## Step 4: Upload to GoDaddy

cPanel → File Manager

Open:

```text
public_html
```

ZIP upload karein aur extract karein.

---

## Step 5: Database Setup

cPanel → MySQL Databases

Create:

* Database
* Database User
* Password

Update `.env`

```env
DB_CONNECTION=mysql
DB_HOST=localhost
DB_PORT=3306

DB_DATABASE=your_database
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

---

## Step 6: Permissions

If required:

```text
storage/
bootstrap/cache/
```

Set:

```text
755
```

or

```text
775
```

---

## Step 7: Verify Build Files

Check:

```text
public/build/manifest.json
```

Must exist.

---

## Step 8: Test Website

Open:

```text
https://yourdomain.com
```

Check:

* Home Page
* Login
* Register
* Dashboard
* Payments
* Database Connection

---

# GitHub Actions CI/CD Deployment

## GitHub Secrets

Repository → Settings → Secrets and variables → Actions

Add:

```text
FTP_SERVER
FTP_USERNAME
FTP_PASSWORD
```

---

## Workflow Location

Create:

```text
.github/workflows/deploy.yml
```

---

## Deployment Workflow

```yaml
name: Deploy Laravel 12

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: shivammathur/setup-php@v2
        with:
          php-version: '8.3'

      - run: composer install --no-dev --optimize-autoloader

      - uses: actions/setup-node@v4
        with:
          node-version: 20

      - run: npm install

      - run: npm run build

      - uses: SamKirkland/FTP-Deploy-Action@v4.3.5
        with:
          server: ${{ secrets.FTP_SERVER }}
          username: ${{ secrets.FTP_USERNAME }}
          password: ${{ secrets.FTP_PASSWORD }}
          local-dir: ./
          server-dir: /public_html/

          exclude: |
            **/.git*
            **/.git*/**
            **/node_modules/**
            **/.github/**
            **/.env
            **/.env.*
```

---

## Deploy Changes

```bash
git add .
git commit -m "update"
git push origin main
```

GitHub Actions automatically:

1. Composer Install
2. NPM Install
3. Vite Build
4. FTP Upload

---

# Common Errors

## Vite Manifest Not Found

Run:

```bash
npm run build
```

Verify:

```text
public/build/manifest.json
```

---

## 500 Internal Server Error

Check:

```env
APP_DEBUG=true
```

Temporarily enable and inspect error.

---

## Database Connection Error

Verify:

```env
DB_DATABASE
DB_USERNAME
DB_PASSWORD
```

---

## Permission Error

Set:

```text
storage = 775
bootstrap/cache = 775
```

---

# Deployment Checklist

* [ ] composer install --no-dev
* [ ] npm install
* [ ] npm run build
* [ ] vendor folder exists
* [ ] public/build exists
* [ ] database configured
* [ ] APP_KEY exists
* [ ] website tested
* [ ] GitHub secrets added
* [ ] GitHub Actions working

