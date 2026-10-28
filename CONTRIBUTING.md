# Contributing to Arranca del Aluvión

Thank you for your interest in contributing! This document provides guidelines and instructions for developers who want to contribute to the project.

## Code of Conduct

- Be respectful and inclusive
- Focus on code quality and user experience
- Provide constructive feedback
- Help others learn and grow

## Getting Started

### Prerequisites

- Node.js 18 or higher
- Python 3.8 or higher
- Git
- A modern code editor (VSCode recommended)

### Setup Development Environment

```bash
# Clone the repository
git clone https://github.com/yourusername/arranca-del-aluvion.git
cd arranca-del-aluvion

# Install dependencies (if any)
npm install

# Build the game
python3 build.py

# Open in browser
# - Use a local web server: python3 -m http.server 8000
# - Then visit: http://localhost:8000/arranca-del-aluvion.html
```

## Project Structure

```
src/
├── game.js         # Core game logic, simulation loop, HUD
├── gamecore.js     # Car physics, hazard systems, levels
├── world.js        # Map definition, street geometry
├── app.js          # Application entry point, initialization
└── sim.js          # Simulation utilities

qa/
├── run_game.py     # Full game runner
├── run_design.py   # Design system tests
├── run_tris.py     # Triangle count profiler
└── run_hazards.py  # Hazard spawning tests
```

## Development Workflow

### 1. Create a Feature Branch

```bash
git checkout -b feature/your-feature-name
# or
git checkout -b fix/bug-description
```

Use descriptive branch names:
- `feature/gamepad-support`
- `fix/mobile-touch-controls`
- `docs/api-documentation`

### 2. Make Your Changes

- Edit files in the `src/` directory
- Keep changes focused and atomic
- Write clear, maintainable code
- Add comments for complex logic

### 3. Build and Test

```bash
# Build the game
python3 build.py

# Test in browser
python3 -m http.server 8000
# Visit http://localhost:8000/arranca-del-aluvion.html

# Run specific tests
python3 qa/run_game.py
python3 qa/run_hazards.py
python3 qa/run_tris.py
```

### 4. Check Performance

The game must maintain **60 FPS on mobile**:

```bash
# Profile triangle count
python3 qa/run_tris.py
# Should output: ~2.1M triangles at default LOD

# Monitor frame time
# Use browser DevTools → Performance tab
# Target: 16.67ms per frame (60 FPS)
```

### 5. Commit with Clear Messages

```bash
git add .
git commit -m "feat: add gamepad support for steering"
```

**Commit Message Format:**

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Formatting, missing semicolons
- `refactor`: Code restructuring
- `perf`: Performance improvement
- `test`: Adding tests
- `chore`: Build process, dependencies

**Example:**

```
feat(controls): add analog stick support

- Implement axis mapping for gamepad triggers
- Add deadzone filtering (0.15)
- Smooth interpolation for steering input

Closes #42
```

### 6. Push and Create Pull Request

```bash
git push origin feature/your-feature-name
```

Then create a Pull Request on GitHub with:
- Clear title
- Description of changes
- Before/after screenshots (for visual changes)
- Test results

## Code Style

### JavaScript

- Use **ES6+ syntax** (const/let, arrow functions, destructuring)
- **No semicolons** (project standard)
- **2-space indentation**
- **camelCase** for variables and functions
- **PascalCase** for classes and constructors
- Add comments for non-obvious logic

**Example:**

```javascript
// Create a car with physics
const createCar = () => {
  const car = {
    x: 0,
    z: 0,
    v: 0,           // velocity
    h: 0,           // heading angle
    depth: 0,       // damage level
    crashes: 0      // collision count
  }
  return car
}
```

### Python

- Follow **PEP 8** style guide
- Use **4-space indentation**
- Add docstrings to functions
- Use type hints where helpful

```python
def build_game(input_file: str, output_file: str) -> bool:
  """Build the game from source files."""
  # Implementation
  return True
```

### HTML/CSS

- Use **semantic HTML5** elements
- CSS custom properties for theming (see `:root`)
- **BEM** naming for components (when applicable)
- Mobile-first responsive design

## Testing

### Manual Testing Checklist

Before submitting a PR, test:

- ✅ **Desktop**: Chrome, Firefox, Safari (latest versions)
- ✅ **Mobile**: iOS Safari, Chrome Android
- ✅ **Performance**: 60 FPS maintained
- ✅ **Controls**: Keyboard, touch, gamepad
- ✅ **Gameplay**: All 3 routes completable
- ✅ **Edge cases**: Extreme speeds, multiple hazards

### Automated Testing

```bash
# Run triangle count test
python3 qa/run_tris.py
# Should show: ~2.1M triangles

# Run hazard generation test
python3 qa/run_hazards.py
# Should show hazard distribution

# Full game test
python3 qa/run_game.py
# Should complete all routes successfully
```

## Performance Guidelines

- **Maximum triangle count**: 2.5M (before LOD kicks in)
- **Minimum frame rate**: 60 FPS on Pixel 4a / iPhone 12
- **Maximum memory**: 150 MB during gameplay
- **File size**: Keep compiled HTML under 250 KB

## Documentation

When adding features, please update:

1. **README.md**: Add to Features section if user-facing
2. **Code comments**: Explain non-obvious algorithms
3. **CONTRIBUTING.md**: Update if workflow changes

## Areas for Contribution

### High Priority 🔴

- [ ] Sound effects system (engine, collision, UI sounds)
- [ ] Music integration
- [ ] Leaderboard backend
- [ ] Progressive Web App (PWA) manifest

### Medium Priority 🟡

- [ ] Gamepad/joystick full support
- [ ] Mobile gyroscope steering
- [ ] Accessibility improvements (ARIA labels, color contrast)
- [ ] Performance profiling tools

### Low Priority 🟢

- [ ] Additional routes/maps
- [ ] Alternative car models
- [ ] Unlockable themes
- [ ] Replay system

## Reporting Bugs

If you find a bug, create an issue with:

1. **Title**: Clear, descriptive
2. **Description**: What happened vs. expected
3. **Steps to Reproduce**: Numbered list
4. **Environment**: Browser, OS, device
5. **Screenshots**: If visual issue

**Template:**

```markdown
## Bug: [Title]

### Description
What should happen vs. what actually happens

### Steps to Reproduce
1. Start game
2. Drive to location X
3. Crash into obstacle
4. Observe issue

### Environment
- Browser: Chrome 120
- OS: Windows 11
- Device: Desktop

### Expected
Car should bounce off obstacle

### Actual
Car passes through obstacle

### Screenshots
[Attach if relevant]
```

## Feature Requests

Suggest features by opening an issue with:

- **Use case**: Why is this needed?
- **Proposed solution**: How should it work?
- **Alternatives**: Other approaches?
- **Priority**: Nice-to-have or essential?

## Review Process

1. **Automated checks** (code style, build)
2. **Manual review** (functionality, performance)
3. **Testing** (desktop, mobile)
4. **Approval** and merge

Reviewers will:
- Check code quality
- Verify performance impact
- Test on multiple devices
- Request changes if needed

## Licensing

By contributing, you agree that your code is licensed under the MIT License. This ensures the project remains open and accessible.

## Questions?

- 📧 Email: j.carcamo.bustamante@gmail.com
- 💬 GitHub Issues: For technical questions
- 🐛 GitHub Discussions: For general discussion

---

Happy contributing! 🚗
