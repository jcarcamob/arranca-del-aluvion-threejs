# Quick Start Guide - Arranca del Aluvión

Get the game running in 5 minutes or publish it online in 2 minutes.

## 🎮 Play the Game (Right Now!)

### Option 1: Online (Easiest)
Visit: **[arranca-del-aluvion.netlify.app](https://arranca-del-aluvion.netlify.app)**

Play directly in your browser—no installation needed!

### Option 2: Local (With Your Computer)

**On Windows:**
```bash
# Open Command Prompt and run:
cd C:\path\to\arranca-del-aluvion
python -m http.server 8000

# Then open in browser:
# http://localhost:8000/arranca-del-aluvion.html
```

**On Mac/Linux:**
```bash
cd ~/path/to/arranca-del-aluvion
python3 -m http.server 8000

# Then open in browser:
# http://localhost:8000/arranca-del-aluvion.html
```

## 🕹️ How to Play

### Objective
Drive to the Metro station before the mudslide catches you!

### Controls

**Desktop:**
- **← →** or **A/D**: Steer left/right
- **↑** or **W**: Accelerate
- **↓** or **S**: Brake
- **Space**: Toggle mirror (see more road)

**Mobile:**
- **Drag steering wheel** (bottom left): Control direction
- **Tap gas pedal** (bottom right): Accelerate
- **Tap brake** (bottom right): Brake

### Game Status
- **Speed**: Current velocity (km/h)
- **Signal**: Time until you reach the metro
- **Margin**: How much time before mudslide catches you
- **Collisions**: Number of obstacles hit

### Hazards
- 🟦 **Puddles**: Slow you down
- ⬛ **Potholes**: Cause damage
- 🌳 **Trees/Cars**: Crash obstacles

## 🚀 Publish Your Game (2 Minutes)

### Publish to Netlify (Recommended)

1. Download the file: `arranca-del-aluvion.html`
2. Go to **[netlify.com](https://netlify.com)**
3. Create a free account
4. Drag & drop the HTML file
5. **Done!** You have a live URL 🎉

### Publish to GitHub Pages

1. Create a GitHub repository named `arranca-del-aluvion`
2. Upload `arranca-del-aluvion.html` as `index.html`
3. Go to **Settings → Pages**
4. Select "Deploy from branch" → `main` branch
5. Your game is live at: `https://yourusername.github.io/arranca-del-aluvion`

### Publish to itch.io

1. Go to **[itch.io](https://itch.io)** and create account
2. Click "Dashboard" → "New Project"
3. Upload HTML file
4. Set as "HTML" type
5. Your game: `https://yourusername.itch.io/arranca-del-aluvion`

## 🛠️ Develop Locally

### Prerequisites
- Python 3.8+
- Modern code editor (VSCode recommended)
- Git (optional)

### Setup

```bash
# Clone or download the project
git clone https://github.com/yourusername/arranca-del-aluvion.git
cd arranca-del-aluvion

# (No npm install needed—it's a pure JavaScript project!)
```

### Modify & Build

**Edit source files:**
- `src/game.js` - Game logic, physics
- `src/gamecore.js` - Car system, hazards
- `src/world.js` - Map definition
- `src/game.src.html` - HTML template

**Build the game:**
```bash
python3 build.py
# Creates: arranca-del-aluvion.html
```

**Test locally:**
```bash
python3 -m http.server 8000
# Visit: http://localhost:8000/arranca-del-aluvion.html
```

## 📊 Project Structure

```
arranca-del-aluvion/
├── arranca-del-aluvion.html      ← Game file (play this!)
├── README.md                      ← Full documentation
├── CONTRIBUTING.md                ← How to contribute
├── DEVELOPMENT.md                 ← Technical guide
├── ARCHITECTURE.md                ← System design
├── LICENSE                        ← MIT License
│
├── src/
│   ├── game.js                   ← Game loop
│   ├── gamecore.js               ← Physics & systems
│   ├── world.js                  ← Map definition
│   ├── app.js                    ← Entry point
│   └── game.src.html             ← HTML template
│
├── build.py                       ← Build script
└── qa/
    ├── run_game.py               ← Test runner
    ├── run_hazards.py            ← Hazard tests
    └── run_tris.py               ← Performance check
```

## 🎨 Customize the Game

### Change Colors

Edit `src/game.js` and look for `:root`:

```javascript
// In game.src.html <style> section
:root {
  --bg: #11161b;           // Background
  --alert: #ff5546;        // Danger color
  --mud: #c98f4e;          // Mudslide color
  --ok: #8fd0a8;           // Success green
}
```

### Adjust Difficulty

Edit `src/gamecore.js`:

```javascript
const LEVELS = {
  'LEVEL_1': { density: 40, timeout: 180 },
  'LEVEL_2': { density: 60, timeout: 120 },
  'LEVEL_3': { density: 100, timeout: 90 }
}
```

### Change Car Speed

Edit `src/gamecore.js`:

```javascript
const MAX_SPEED = 30      // m/s (currently 108 km/h)
const ACCEL = 45          // acceleration
const BRAKE = 60          // deceleration
```

## 🐛 Troubleshooting

### Game won't load
- Check browser: Must support WebGL 2
- Clear browser cache: Ctrl+Shift+Delete
- Try different browser: Chrome, Firefox, Safari

### Low FPS on mobile
- Close other apps
- Reduce screen brightness
- Try toggling mirror (⚙️ button)
- Update your browser

### Controls not working
- Try keyboard controls first
- On mobile: Allow touch permissions
- Check device orientation (landscape recommended)

### File size too large
- Check your ISP isn't limiting
- Try downloading at different time
- File should be ~176 KB only

## 📚 Learn More

- **[Full README](README.md)** - Complete documentation
- **[Development Guide](DEVELOPMENT.md)** - Technical details
- **[Architecture](ARCHITECTURE.md)** - System design
- **[Contributing](CONTRIBUTING.md)** - How to contribute

## 🎯 Next Steps

1. ✅ **Play the game** → Try all 3 routes
2. ✅ **Publish online** → Share with friends
3. ✅ **Read README.md** → Understand the project
4. ✅ **Modify code** → Customize the game
5. ✅ **Contribute** → Submit improvements

## 📞 Need Help?

- 🐛 **Found a bug?** → Open an issue on GitHub
- 💡 **Have an idea?** → Suggest a feature
- 📧 **Want to chat?** → Email: j.carcamo.bustamante@gmail.com

## 🎮 Tips & Tricks

- **Speed control**: Touch gas/brake gradually for better precision
- **Curve technique**: Start turning early, ease into curves
- **Route selection**: Route A is easier, Route C is hardest
- **Level scaling**: Each level has more hazards—adapt your strategy
- **Mirror toggle**: Turn off mirror if you need more view space

---

**Have fun!** 🚗💨

Made in Santiago, Chile with ❤️
