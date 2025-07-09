# 🎵 Morse Code Translator

A modern, interactive web application for translating text to Morse code and vice versa. Built with Vue.js and styled with Tailwind CSS.

![Morse Code Translator](https://img.shields.io/badge/Vue.js-3.x-4FC08D?style=for-the-badge&logo=vue.js&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)

## ✨ Features

- **Bidirectional Translation**: Convert text to Morse code and Morse code back to text
- **Real-time Processing**: Live translation as you type
- **Audio Playback**: Listen to your Morse code with adjustable speed (WPM)
- **Interactive Reference**: Built-in Morse code character reference chart
- **Copy to Clipboard**: Easy one-click copying of results
- **Quick Examples**: Pre-loaded examples for testing
- **Responsive Design**: Works seamlessly on desktop and mobile devices
- **Dark Theme**: Easy on the eyes with a modern dark interface

## 🚀 Demo

Visit the live demo: [Morse Code Translator](https://morse-web.reversed.dev)

## 📦 Installation

### Prerequisites

- Node.js (v20 or higher)
- pnpm (recommended) or npm

### Setup

1. Clone the repository:
```bash
git clone https://github.com/Space-Banane/morse-web.git
cd morse-web
```

2. Install dependencies:
```bash
pnpm install
```

3. Start the development server:
```bash
pnpm run serve
```

4. Open your browser and navigate to `http://localhost:8080`

## 🛠️ Available Scripts

### Development
```bash
pnpm run serve
```
Compiles and hot-reloads for development

### Production Build
```bash
pnpm run build
```
Compiles and minifies for production

### Linting
```bash
pnpm run lint
```
Lints and fixes files

## 📖 Usage

### Text to Morse Code
1. Select "Text to Morse" mode (default)
2. Type your message in the input field
3. The Morse code will appear instantly in the output panel
4. Click "Play" to hear the audio representation
5. Use the "Copy" button to copy the result

### Morse Code to Text
1. Select "Morse to Text" mode
2. Enter Morse code using dots (.) and dashes (-)
3. Use spaces to separate letters
4. Use forward slashes (/) to separate words
5. The decoded text appears in the output panel

### Audio Controls
- **Play/Stop**: Listen to the Morse code audio
- **Speed Control**: Adjust playback speed from 5 to 50 WPM
- **Real-time Feedback**: Visual indication during playback

## 🎯 Supported Characters

The translator supports:
- **Letters**: A-Z (case insensitive)
- **Numbers**: 0-9
- **Punctuation**: . , ? ' ! / ( ) & : ; = + - _ " $ @

## 🏗️ Technical Details

### Built With
- **Vue.js 3**: Progressive JavaScript framework
- **Tailwind CSS**: Utility-first CSS framework
- **morse-node**: Morse code encoding/decoding library
- **Web Audio API**: For audio playback functionality

### Project Structure
```
morse-web/
├── src/
│   ├── components/
│   │   └── MorseCodeApp.vue    # Main application component
│   ├── assets/
│   │   └── morse_icon.png      # Application icon
│   ├── App.vue                 # Root component
│   └── main.js                 # Application entry point
├── public/
│   └── index.html              # HTML template
├── package.json                # Dependencies and scripts
└── README.md                   # This file
```

### Key Features Implementation
- **Real-time Translation**: Uses Vue.js watchers for instant feedback
- **Audio Generation**: Web Audio API with sine wave oscillators
- **Responsive Design**: Tailwind CSS grid system and responsive utilities
- **Smooth Animations**: CSS transitions and Vue.js transition components

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

### Development Guidelines
1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Commit your changes: `git commit -m 'Add amazing feature'`
4. Push to the branch: `git push origin feature/amazing-feature`
5. Open a Pull Request

### Code Style
- Follow Vue.js style guide
- Use Tailwind CSS for styling
- Maintain consistent indentation (2 spaces)
- Add comments for complex logic

## 🐛 Known Issues

- Audio playback may not work on some older browsers
- Very long messages may cause performance issues
- Some special characters are not supported

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [morse-node](https://github.com/ozdemirburak/morse-node) - Morse code encoding/decoding library
- [Tailwind CSS](https://tailwindcss.com/) - For the beautiful styling
- [Vue.js](https://vuejs.org/) - For the reactive framework
- Samuel Morse - For inventing Morse code

## 📧 Contact

**Space** - [space.reversed.dev](https://space.reversed.dev)

Project Link: [https://github.com/Space-Banane/morse-web](https://github.com/Space-Banane/morse-web)

---

*Built with ❤️ by [Space](https://space.reversed.dev)*
