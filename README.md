# Mods Insider - Smash Remix Mods Hub

A Material Design documentation site showcasing Smash Remix mods and the home of Smash Remix Redux.

## Building Locally

### Prerequisites
- Python 3.8+
- pip (Python package manager)

### Setup

```bash
# Install dependencies
pip install -r requirements.txt

# Serve locally (http://localhost:8000)
mkdocs serve
```

### Building for deployment

```bash
mkdocs build
```

This creates a `site/` folder with static HTML ready to deploy.

## Project Structure

```
docs/
├── index.md              # Home page
├── redux/                # Redux mod section
│   └── index.md
├── mods/                 # Individual mod pages
│   ├── index.md
│   ├── slippy.md
│   ├── project-x.md
│   ├── spiderman.md
│   ├── shino.md
│   ├── knuckles.md
│   └── galleon.md
├── docs/                 # Documentation
│   ├── index.md
│   ├── installation.md
│   ├── xdelta.md
│   └── troubleshooting.md
└── gallery/              # Screenshots gallery
    └── index.md
```

## Adding Mod Content

1. Edit the relevant mod file (e.g., `docs/mods/slippy.md`)
2. Fill in description, features, download links, and credits
3. Add screenshots to `docs/gallery/screenshots/`
4. Reference them in the mod's page

## Deployment

This site can be deployed to:
- GitHub Pages
- Netlify
- Vercel
- Any static host supporting `mkdocs build`

For GitHub Pages:
```bash
mkdocs gh-deploy
```

---

**Made with ❤️ for the Smash Remix community**
