# GitHub Setup Instructions

Complete step-by-step guide to publish "Arranca del Aluvión" on GitHub.

## 1. Create GitHub Repository

### Option A: Using GitHub Web Interface

1. Go to **github.com/new**
2. Fill in details:
   - **Repository name**: `arranca-del-aluvion`
   - **Description**: `Three.js arcade racing game - escape a mudslide in Santiago, Chile`
   - **Visibility**: **Public** (so others can play/contribute)
   - **Initialize with**: Skip these (you already have files)
   - **License**: MIT License (we already have it)
3. Click **"Create repository"**
4. Copy the repository URL shown (e.g., `https://github.com/yourusername/arranca-del-aluvion.git`)

### Option B: Using GitHub CLI

```bash
gh auth login  # If not already logged in
gh repo create arranca-del-aluvion --public --source=. --remote=origin --push
```

## 2. Push Code to GitHub

### First Time Setup

```bash
cd /home/claude/aluvion

# Initialize git repository
git init

# Add all files
git add .

# Create initial commit
git commit -m "Initial commit: Arranca del Aluvión - Three.js racing game

- Three.js r185 game engine
- Arcade racing mechanic: escape a mudslide in Santiago
- Procedurally generated city with 2.1M triangles
- Mobile-optimized: 60 FPS on modern devices
- 3 difficulty levels with different routes
- MIT License - open source"

# Set main branch
git branch -M main

# Add remote repository
git remote add origin https://github.com/yourusername/arranca-del-aluvion.git

# Push to GitHub
git push -u origin main
```

### Future Updates

```bash
# Make changes to files...

# Add changed files
git add .

# Commit with message
git commit -m "feat: add feature description"

# Push to GitHub
git push origin main
```

## 3. Enable GitHub Pages (Optional)

This lets people play directly from GitHub:

1. Go to your repository on GitHub
2. Click **Settings** (top right)
3. Click **Pages** (left sidebar)
4. Under "Build and deployment":
   - **Source**: Select "Deploy from a branch"
   - **Branch**: Select `main`
   - **Folder**: Select `/ (root)`
5. Click **Save**
6. Wait 1-2 minutes
7. Your game is live at: `https://yourusername.github.io/arranca-del-aluvion`

## 4. Configure Repository Settings

### Add Topics (for discovery)

1. Go to repository main page
2. Click ⚙️ **Settings** (top right)
3. Look for "Topics" section
4. Add these tags:
   - `three-js`
   - `game`
   - `webgl`
   - `javascript`
   - `arcade`
   - `racing`
   - `gamedev`
   - `chile`

### Add Repository Description

1. On main page, click ⚙️ (gear icon, right side)
2. Edit "About" section:
   - **Description**: "Three.js arcade racing game"
   - **Website**: Link to Netlify version or GitHub Pages
   - **Topics**: Add the tags above

### Enable Discussions (Optional)

1. Go to Settings
2. Scroll to "Features"
3. Enable "Discussions" checkbox
4. This allows community discussion

## 5. Create Release (Optional)

```bash
# Tag the release
git tag -a v1.0.0 -m "Version 1.0.0 - Initial release"

# Push tag to GitHub
git push origin v1.0.0
```

Then on GitHub:
1. Go to "Releases" tab
2. Click "Create release from tag"
3. Add release notes:

```markdown
## Version 1.0.0 - Initial Release

### Features
- Three.js r185 game engine
- Procedurally generated Santiago city
- 3 difficulty levels with unique routes
- Physics-based car simulation
- Mobile-optimized (2.1M triangles, 60 FPS)
- MIT License

### Play Online
[Play on Netlify](https://arranca-del-aluvion.netlify.app)
[Play on GitHub Pages](https://yourusername.github.io/arranca-del-aluvion)

### Download
- [arranca-del-aluvion.html](releases/download/v1.0.0/arranca-del-aluvion.html) (176 KB)

### Changes
- Initial public release
- Visual redesign with PBR materials
- Complete documentation
```

## 6. Keep Repository Updated

### Regular Commits

```bash
# Make changes...
git add .
git commit -m "type(scope): description

Detailed explanation of changes.

Fixes #123"

git push origin main
```

**Commit Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation update
- `perf`: Performance improvement
- `refactor`: Code restructuring
- `style`: Formatting changes
- `test`: Adding tests

## 7. Share Your Repository

### On Social Media

**Twitter/X:**
```
🚗 Just published my Three.js arcade game "Arranca del Aluvión"!
Escape a mudslide in Santiago while navigating obstacles.
3 difficulty levels, mobile-optimized, completely free & open source! 
🌐 https://github.com/yourusername/arranca-del-aluvion
🎮 https://arranca-del-aluvion.netlify.app
#gamedev #threejs #chile
```

**Reddit:**
Post to:
- r/gamedev - "Show & Tell Saturday"
- r/webgames - "Showcase your web game"
- r/chile - "A Chilean developer here..."
- r/OpenSource - "Open source Three.js game"

**LinkedIn:**
```
Excited to share my passion project: "Arranca del Aluvión", a Three.js 
arcade racing game where you escape a mudslide in Santiago.

Highlights:
✅ 176 KB, fully self-contained
✅ 60 FPS on mobile
✅ MIT License - completely open source
✅ Full documentation for developers

Play online and contribute on GitHub!
```

## 8. Attract Contributors

### Good Issues for First-Time Contributors

```markdown
## good first issue

Great for newcomers! Pick one:

- [ ] Add sound effects system
- [ ] Implement gamepad support
- [ ] Add background music
- [ ] Create accessibility improvements
- [ ] Add more routes
- [ ] Improve mobile UI
```

### Discussions for Ideas

Go to **Discussions** tab and create:

```markdown
## Feature Ideas & Discussion

What would you like to see in the game?

- New routes through Santiago?
- Different vehicles?
- Multiplayer racing?
- Different environments?
```

## 9. Continuous Integration (Optional)

### Setup GitHub Actions

Create `.github/workflows/build.yml`:

```yaml
name: Build

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: '3.9'
      - name: Build game
        run: python3 build.py
      - name: Check file size
        run: |
          SIZE=$(stat -f%z arranca-del-aluvion.html 2>/dev/null || stat -c%s arranca-del-aluvion.html)
          echo "Game size: $SIZE bytes"
          if [ $SIZE -gt 250000 ]; then echo "WARNING: Game too large!"; fi
```

This automatically builds the game on every push.

## 10. Final Checklist

- [ ] Repository created on GitHub
- [ ] Code pushed to `main` branch
- [ ] README.md visible on repository
- [ ] QUICKSTART.md link working
- [ ] LICENSE file present (MIT)
- [ ] Topics added (3-5 relevant tags)
- [ ] GitHub Pages enabled (if desired)
- [ ] Release created with game file
- [ ] Social media posts shared
- [ ] Contributing.md reviewed by friends

## Troubleshooting

### "Authentication failed"

```bash
# If using HTTPS, update credentials
git remote set-url origin https://github.com/yourusername/arranca-del-aluvion.git

# Or use SSH (requires SSH key setup)
git remote set-url origin git@github.com:yourusername/arranca-del-aluvion.git
```

### "File too large"

GitHub has a 100 MB file limit. Your game is only 176 KB, so no issue!

```bash
# Check file sizes
ls -lh arranca-del-aluvion.html
# Should show: -rw-r--r-- 1 ... 176K ...
```

### "Branch protection error"

If you get a protection error:
1. Go to Settings → Branches
2. No branch protection rules should be set for new repo

## Next Steps

1. ✅ Create repository
2. ✅ Push code
3. ✅ Enable Pages
4. ✅ Share on social media
5. ✅ Invite friends to contribute
6. ✅ Monitor discussions/issues
7. ✅ Release updates regularly

## Resources

- [GitHub Guides](https://guides.github.com/)
- [GitHub CLI Reference](https://cli.github.com/)
- [GitHub Pages Documentation](https://pages.github.com/)
- [How to Contribute](CONTRIBUTING.md)

---

**Questions?** Open an issue on GitHub or email j.carcamo.bustamante@gmail.com
