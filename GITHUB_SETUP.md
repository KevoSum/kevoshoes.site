# GitHub Upload Instructions

## Step 1: Install Git
Download and install Git for Windows from: https://git-scm.com/download/win

After installation, restart your terminal.

## Step 2: Configure Git (First Time Only)
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

## Step 3: Initialize Your Repository
```bash
cd "c:\Users\KELVIN\Desktop\kevo project"
git init
```

## Step 4: Add All Files (Respecting .gitignore)
```bash
git add .
```

This will add only files that are NOT in .gitignore:
- ✅ All source code (backend/, frontend/src)
- ✅ Configuration files (package.json, requirements.txt, netlify.toml)
- ✅ Documentation (README.md, guides, etc.)
- ✅ index.html
- ❌ node_modules/ (excluded)
- ❌ __pycache__/ (excluded)
- ❌ .env files (excluded)
- ❌ build/ folders (excluded)

## Step 5: Commit Your Code
```bash
git commit -m "Initial commit: Full stack e-commerce project"
```

## Step 6: Create Repository on GitHub
1. Go to https://github.com/new
2. Create a new repository named "kevo-ecommerce"
3. Do NOT initialize with README (we already have one)
4. Click "Create repository"

## Step 7: Push to GitHub
```bash
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/kevo-ecommerce.git
git push -u origin main
```

Replace `YOUR_USERNAME` with your actual GitHub username.

## What Gets Uploaded?
✅ Source code
✅ Configuration files
✅ Documentation
✅ package.json & requirements.txt (for dependencies, not the packages themselves)

## What Does NOT Get Uploaded?
❌ node_modules/
❌ __pycache__/
❌ .env files
❌ venv/ or env/
❌ build/ or dist/
❌ .vscode/ settings
❌ IDE files

This keeps your repository clean and focused on source code only!

## Verify What Will Be Uploaded
```bash
git status
```

This shows all files that will be committed.
