# ✨ Color Palette Generator

A beautiful, interactive web application for generating and saving color palettes.

## Features

- 🎨 **Random Palette Generation**: Generate beautiful color palettes with a single click
- 🔒 **Lock Colors**: Lock individual colors to keep them while regenerating others
- 📋 **Copy to Clipboard**: Click any color card to copy its hex code
- 💾 **Save Palettes**: Save your favorite palettes to local storage
- ⌨️ **Keyboard Shortcuts**: Press SPACE to generate a new palette
- 📱 **Responsive Design**: Works beautifully on desktop and mobile devices
- ✨ **Smooth Animations**: Engaging animations and transitions throughout

## How to Use

1. **Open the app**: Simply open `index.html` in your web browser
2. **Generate palettes**: Click "Generate New Palette" or press SPACE
3. **Lock colors**: Click the lock icon (🔓) on any color to lock it
4. **Copy colors**: Click on a color card to copy its hex code to clipboard
5. **Save palettes**: Click "Save Palette" to save your current palette
6. **Load saved palettes**: Click on any saved palette to load it

## Running the App

### Option 1: Direct File Access
Simply double-click on `index.html` or open it in your browser.

### Option 2: Local Server
For a better experience, run a local server:

```bash
# Using Python 3
python -m http.server 8000

# Using Node.js (http-server)
npx http-server

# Using PHP
php -S localhost:8000
```

Then navigate to `http://localhost:8000` in your browser.

## Technologies Used

- Pure HTML5
- CSS3 (with animations and gradients)
- Vanilla JavaScript (no dependencies!)
- LocalStorage API for persistence

## Browser Support

Works in all modern browsers that support:
- CSS Grid
- CSS Animations
- LocalStorage
- Clipboard API

Enjoy creating beautiful color palettes! 🎨
