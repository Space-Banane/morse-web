<template>
  <div class="min-h-screen bg-slate-900 text-slate-200">
    <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8 py-12 sm:py-16">
      <!-- Header -->
      <header class="text-center mb-12">
        <div class="flex items-center justify-center gap-4 mb-3">
          <img src="@/assets/morse_icon.png" alt="Morse Code" class="w-12 h-12 object-contain">
          <h1 class="text-4xl md:text-5xl font-bold text-white">
            Morse Code Translator
          </h1>
        </div>
        <p class="text-slate-400 text-lg">A modern tool to convert text to morse code and back.</p>
      </header>

      <!-- Main Content -->
      <main class="space-y-10">
        <!-- Mode Toggle -->
        <div class="flex justify-center p-1 bg-slate-800 rounded-full max-w-sm mx-auto">
          <button
            :class="['w-1/2 py-2.5 text-sm font-semibold rounded-full transition-colors duration-300', currentMode === 'encode' ? 'bg-blue-600 text-white' : 'text-slate-400 hover:bg-slate-700/50']"
            @click="setMode('encode')"
          >
            Text to Morse
          </button>
          <button
            :class="['w-1/2 py-2.5 text-sm font-semibold rounded-full transition-colors duration-300', currentMode === 'decode' ? 'bg-blue-600 text-white' : 'text-slate-400 hover:bg-slate-700/50']"
            @click="setMode('decode')"
          >
            Morse to Text
          </button>
        </div>

        <!-- Input/Output Grid -->
        <div class="grid md:grid-cols-2 gap-8">
          <!-- Input Section -->
          <div class="bg-slate-800/50 rounded-2xl p-6 border border-slate-700/80">
            <label :for="'input-' + currentMode" class="block mb-3 font-semibold text-slate-300 text-base">
              {{ currentMode === "encode" ? "Your Text" : "Morse Code" }}
            </label>
            <textarea
              :id="'input-' + currentMode"
              v-model="inputText"
              :placeholder="
                currentMode === 'encode'
                  ? 'Enter your message...'
                  : 'Enter morse code...'
              "
              class="w-full h-40 p-4 border border-slate-700 rounded-lg font-mono text-base resize-none transition duration-200 focus:outline-none focus:border-blue-500 focus:ring-2 focus:ring-blue-500/50 bg-slate-900 text-white shadow-inner placeholder:text-slate-500"
              @input="handleInput"
            ></textarea>
            <div v-if="currentMode === 'decode'" class="mt-3 text-slate-400 text-xs">
              Use <code class="bg-slate-700 px-1.5 py-0.5 rounded">.</code> and <code class="bg-slate-700 px-1.5 py-0.5 rounded">-</code>, spaces between letters, and <code class="bg-slate-700 px-1.5 py-0.5 rounded">/</code> between words.
            </div>
          </div>

          <!-- Output Section -->
          <div class="bg-slate-800/50 rounded-2xl p-6 border border-slate-700/80">
            <label class="block mb-3 font-semibold text-slate-300 text-base">
              {{ currentMode === "encode" ? "Morse Code" : "Decoded Text" }}
            </label>
            <div class="relative bg-slate-900 border border-slate-700 rounded-lg h-40 font-mono text-base shadow-inner">
              <div class="h-full overflow-y-auto p-4 break-words">
                <div :class="[outputText ? 'text-white' : 'text-slate-500 italic']">
                  {{ outputText || "Result will appear here..." }}
                </div>
              </div>
              <button
                v-if="outputText"
                @click="copyToClipboard"
                class="absolute top-2.5 right-2.5 px-3 py-1 bg-slate-700 text-slate-300 rounded-md text-xs font-medium transition hover:bg-slate-600 focus:outline-none focus:ring-2 focus:ring-slate-500/50 z-10"
                :class="{ 'bg-green-600 text-white': justCopied }"
              >
                {{ justCopied ? "Copied!" : "Copy" }}
              </button>
            </div>
          </div>
        </div>

        <!-- Audio Controls -->
        <div v-if="currentMode === 'encode' && outputText" class="bg-slate-800/50 rounded-2xl p-6 border border-slate-700/80">
          <h3 class="text-base font-semibold text-slate-300 mb-4">Audio Playback</h3>
          <div class="flex items-center gap-6 flex-wrap">
            <div class="flex gap-3">
              <button @click="playMorseAudio" class="px-5 py-2 bg-blue-600 text-white rounded-lg text-sm font-semibold transition hover:bg-blue-700 disabled:bg-slate-600 disabled:cursor-not-allowed shadow-sm flex items-center gap-2" :disabled="isPlaying">
                <span v-if="!isPlaying">▶</span>
                {{ isPlaying ? "Playing..." : "Play" }}
              </button>
              <button v-if="isPlaying" @click="stopMorseAudio" class="px-5 py-2 bg-slate-600 text-white rounded-lg text-sm font-semibold transition hover:bg-slate-700 shadow-sm flex items-center gap-2">
                <span>■</span> Stop
              </button>
            </div>
            <div class="flex items-center gap-3 text-slate-300">
              <label class="text-sm font-medium">Speed (WPM):</label>
              <input
                type="range"
                v-model="audioSpeed"
                min="100"
                max="500"
                step="50"
                class="w-32 accent-blue-600"
              />
              <span class="font-mono text-xs bg-slate-700 px-2 py-1 rounded-md">{{ Math.round(1200 / (audioSpeed / 50)) }} WPM</span>
            </div>
          </div>
        </div>

        <!-- Character Reference -->
        <div class="bg-slate-800/50 rounded-2xl border border-slate-700/80 overflow-hidden">
          <button
            @click="showReference = !showReference"
            class="w-full py-4 px-6 bg-slate-800 text-slate-300 text-base font-semibold transition hover:bg-slate-700/50 flex items-center justify-between"
          >
            <span>Morse Code Reference</span>
            <span class="text-lg transition-transform duration-300" :class="{'rotate-180': showReference}">▼</span>
          </button>
          <transition name="slide">
            <div v-if="showReference" class="p-6 border-t border-slate-700/80">
              <div class="grid grid-cols-4 sm:grid-cols-6 md:grid-cols-8 lg:grid-cols-10 gap-3">
                <div
                  v-for="(morse, char) in morseReference"
                  :key="char"
                  class="flex flex-col items-center p-3 bg-slate-700/50 border border-slate-600/50 rounded-lg"
                >
                  <span class="font-bold text-white text-lg mb-1">{{ char.toUpperCase() }}</span>
                  <span class="font-mono text-slate-300 text-xs">{{ morse }}</span>
                </div>
              </div>
            </div>
          </transition>
        </div>

        <!-- Quick Examples -->
        <div class="bg-slate-800/50 rounded-2xl p-6 border border-slate-700/80">
          <h3 class="text-base font-semibold text-slate-300 mb-4">Quick Examples</h3>
          <div class="flex gap-3 flex-wrap">
            <button
              v-for="example in examples"
              :key="example.text"
              @click="useExample(example)"
              class="px-4 py-2 bg-slate-700 text-slate-300 rounded-lg text-sm font-medium transition hover:bg-slate-600 shadow-sm"
            >
              {{ example.text }}
            </button>
          </div>
        </div>
      </main>

      <!-- Explanation Section -->
      <div class="mt-16 space-y-8">
        <div class="bg-slate-800/50 rounded-2xl p-8 border border-slate-700/80">
          <h2 class="text-2xl font-bold text-white mb-6">What is Morse Code?</h2>
          <div class="prose prose-slate max-w-none">
            <p class="text-slate-300 mb-4 leading-relaxed">
              Morse code is a communication system that uses a series of on-off signals—like tones, lights, or clicks—to represent letters, numbers, and punctuation. Developed by Samuel Morse in the 1830s, it uses short signals called "dots" (·) and long signals called "dashes" (–) to form characters.
            </p>
            <p class="text-slate-300 mb-4 leading-relaxed">Each character has a unique combination of dots and dashes. For example:</p>
            <div class="bg-slate-900 border border-slate-700 rounded-lg p-4 mb-4">
              <pre class="text-slate-200 font-mono text-sm"><code>A = ·−
B = −···
S = ···
O = −−−</code></pre>
            </div>
            <p class="text-slate-300 leading-relaxed">The famous distress signal, SOS, is <code class="bg-slate-700 text-slate-200 px-2 py-1 rounded font-mono text-sm">···−−−···</code> in Morse code.</p>
          </div>
        </div>

        <div class="bg-slate-800/50 rounded-2xl p-8 border border-slate-700/80">
          <h2 class="text-2xl font-bold text-white mb-6">About This Morse Code Translator</h2>
          <div class="prose prose-slate max-w-none">
            <p class="text-slate-300 mb-4 leading-relaxed">This Morse Code Translator is a simple tool to convert text to Morse code and back. It supports:</p>
            <ul class="text-slate-300 space-y-2 mb-6">
              <li class="flex items-start">
                <span class="text-blue-400 mr-2">•</span>
                All 26 English letters (A-Z) and numbers (0-9).
              </li>
              <li class="flex items-start">
                <span class="text-blue-400 mr-2">•</span>
                Common punctuation marks.
              </li>
              <li class="flex items-start">
                <span class="text-blue-400 mr-2">•</span>
                Real-time, bidirectional translation.
              </li>
              <li class="flex items-start">
                <span class="text-blue-400 mr-2">•</span>
                Audio playback to hear the Morse code.
              </li>
            </ul>
          </div>
        </div>

        <div class="bg-slate-800/50 rounded-2xl p-8 border border-slate-700/80">
          <h2 class="text-2xl font-bold text-white mb-6">How to Use the Translator</h2>
          <div class="grid md:grid-cols-2 gap-8">
            <div>
              <h3 class="text-lg font-semibold text-slate-200 mb-4">Translate Text to Morse Code</h3>
              <ol class="text-slate-300 space-y-3">
                <li class="flex items-start">
                  <span class="bg-blue-600 text-white rounded-full w-6 h-6 flex items-center justify-center text-sm font-bold mr-3 mt-0.5 flex-shrink-0">1</span>
                  <span>Type your text into the text input field.</span>
                </li>
                <li class="flex items-start">
                  <span class="bg-blue-600 text-white rounded-full w-6 h-6 flex items-center justify-center text-sm font-bold mr-3 mt-0.5 flex-shrink-0">2</span>
                  <span>The Morse code will appear in the other panel as you type.</span>
                </li>
                <li class="flex items-start">
                  <span class="bg-blue-600 text-white rounded-full w-6 h-6 flex items-center justify-center text-sm font-bold mr-3 mt-0.5 flex-shrink-0">3</span>
                  <span>Click the play button to listen to the generated Morse code.</span>
                </li>
              </ol>
            </div>
            <div>
              <h3 class="text-lg font-semibold text-slate-200 mb-4">Translate Morse Code to Text</h3>
              <ol class="text-slate-300 space-y-3">
                <li class="flex items-start">
                  <span class="bg-blue-600 text-white rounded-full w-6 h-6 flex items-center justify-center text-sm font-bold mr-3 mt-0.5 flex-shrink-0">1</span>
                  <span>Enter Morse code into the Morse code input field.</span>
                </li>
                <li class="flex items-start">
                  <span class="bg-blue-600 text-white rounded-full w-6 h-6 flex items-center justify-center text-sm font-bold mr-3 mt-0.5 flex-shrink-0">2</span>
                  <span>Use a space to separate letters and a forward slash (<code class="bg-slate-700 text-slate-200 px-1.5 py-0.5 rounded font-mono text-xs">/</code>) to separate words.</span>
                </li>
                <li class="flex items-start">
                  <span class="bg-blue-600 text-white rounded-full w-6 h-6 flex items-center justify-center text-sm font-bold mr-3 mt-0.5 flex-shrink-0">3</span>
                  <span>The translated text will appear in the text panel.</span>
                </li>
              </ol>
            </div>
          </div>
          <div class="mt-6 p-4 bg-slate-900/50 border border-slate-700 rounded-lg">
            <p class="text-slate-300"><strong class="text-slate-200">Tip:</strong> You can use periods (<code class="bg-slate-700 text-slate-200 px-1.5 py-0.5 rounded font-mono text-xs">.</code>) for dots and hyphens (<code class="bg-slate-700 text-slate-200 px-1.5 py-0.5 rounded font-mono text-xs">-</code>) for dashes.</p>
          </div>
        </div>

        <div class="bg-slate-800/50 rounded-2xl p-8 border border-slate-700/80">
          <h2 class="text-2xl font-bold text-white mb-6">Why Learn Morse Code?</h2>
          <div class="prose prose-slate max-w-none">
            <p class="text-slate-300 mb-4 leading-relaxed">Morse code is more than just a historical curiosity. It's a valuable skill for:</p>
            <ul class="text-slate-300 space-y-3">
              <li class="flex items-start">
                <span class="text-blue-400 mr-3">•</span>
                <div>
                  <strong class="text-slate-200">Amateur Radio (Ham Radio):</strong> A key part of CW (Continuous Wave) communication.
                </div>
              </li>
              <li class="flex items-start">
                <span class="text-blue-400 mr-3">•</span>
                <div>
                  <strong class="text-slate-200">Emergency Situations:</strong> A reliable backup when modern technology fails.
                </div>
              </li>
              <li class="flex items-start">
                <span class="text-blue-400 mr-3">•</span>
                <div>
                  <strong class="text-slate-200">Education:</strong> A great way to learn about digital communication principles.
                </div>
              </li>
              <li class="flex items-start">
                <span class="text-blue-400 mr-3">•</span>
                <div>
                  <strong class="text-slate-200">Fun:</strong> Send secret messages or use it in puzzles and games.
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>

      <!-- Footer -->
      <footer class="text-center text-slate-500 text-sm pt-12 mt-16 border-t border-slate-800">
        <p class="mb-2">
          Built by
          <a
            href="https://space.reversed.dev"
            target="_blank"
            rel="noopener noreferrer"
            class="text-slate-400 hover:text-blue-400 hover:underline"
          >
            Space
          </a>
          with Vue.js & Tailwind CSS
        </p>
        <p>
          <a
            href="https://github.com/Space-Banane/morse-web"
            target="_blank"
            rel="noopener noreferrer"
            class="text-slate-400 hover:text-blue-400 hover:underline"
          >
            View on GitHub
          </a>
        </p>
      </footer>
    </div>
  </div>
</template>

<script>
const morse = require("morse-node").create("ITU");

export default {
  name: "MorseCodeApp",
  data() {
    return {
      currentMode: "encode", // 'encode' or 'decode'
      inputText: "",
      outputText: "",
      showReference: false,
      showAbout: false,
      isPlaying: false,
      audioContext: null,
      audioTimeoutId: null,
      audioSpeed: 200, // milliseconds
      justCopied: false,
      examples: [
        {
          text: "Hello World",
          morse: ".... . .-.. .-.. --- / .-- --- .-. .-.. -..",
        },
        { text: "SOS", morse: "... --- ..." },
        {
          text: "Good Morning",
          morse: "--. --- --- -.. / -- --- .-. -. .. -. --.",
        },
        { text: "123", morse: ".---- ..--- ...--" },
      ],
      morseReference: {
        a: ".-",
        b: "-...",
        c: "-.-.",
        d: "-..",
        e: ".",
        f: "..-.",
        g: "--.",
        h: "....",
        i: "..",
        j: ".---",
        k: "-.-",
        l: ".-..",
        m: "--",
        n: "-.",
        o: "---",
        p: ".--.",
        q: "--.-",
        r: ".-.",
        s: "...",
        t: "-",
        u: "..-",
        v: "...-",
        w: ".--",
        x: "-..-",
        y: "-.--",
        z: "--..",
        1: ".----",
        2: "..---",
        3: "...--",
        4: "....-",
        5: ".....",
        6: "-....",
        7: "--...",
        8: "---..",
        9: "----.",
        0: "-----",
        ".": ".-.-.-",
        ",": "--..--",
        "?": "..--..",
        "'": ".----.",
        "!": "-.-.--",
        "/": "-..-.",
        "(": "-.--.",
        ")": "-.--.-",
        "&": ".-...",
        ":": "---...",
        ";": "-.-.-.",
        "=": "-...-",
        "+": ".-.-.",
        "-": "-....-",
        _: "..--.-",
        '"': ".-..-.",
        $: "...-..-",
        "@": ".--.-.",
      },
    };
  },
  watch: {
    inputText() {
      this.handleInput();
    },
    currentMode() {
      this.inputText = "";
      this.outputText = "";
    },
  },
  methods: {
    setMode(mode) {
      this.currentMode = mode;
    },

    handleInput() {
      if (!this.inputText.trim()) {
        this.outputText = "";
        return;
      }

      try {
        if (this.currentMode === "encode") {
          // Check if the input is valid for encoding
          if (morse.isValid(this.inputText, "chars")) {
            this.outputText = morse.encode(this.inputText);
          } else {
            this.outputText =
              "Some characters cannot be converted to morse code";
          }
        } else {
          // Decode mode
          if (morse.isValid(this.inputText, "morse")) {
            this.outputText = morse.decode(this.inputText);
          } else {
            this.outputText = "Invalid morse code format";
          }
        }
      } catch (error) {
        this.outputText = "Error: " + error.message;
      }
    },

    async copyToClipboard() {
      try {
        await navigator.clipboard.writeText(this.outputText);
        this.justCopied = true;
        setTimeout(() => {
          this.justCopied = false;
        }, 2000);
      } catch (err) {
        console.error("Failed to copy text: ", err);
      }
    },

    useExample(example) {
      if (this.currentMode === "encode") {
        this.inputText = example.text;
      } else {
        this.inputText = example.morse;
      }
    },

    async playMorseAudio() {
      if (!this.outputText || this.currentMode !== "encode") return;

      this.isPlaying = true;
      this.audioContext = new (window.AudioContext ||
        window.webkitAudioContext)();

      const dotDuration = this.audioSpeed;
      const dashDuration = dotDuration * 3;
      const gapDuration = dotDuration;
      const letterGapDuration = dotDuration * 3;
      const wordGapDuration = dotDuration * 7;

      const frequency = 600; // Hz

      let currentTime = this.audioContext.currentTime;

      for (let i = 0; i < this.outputText.length; i++) {
        const char = this.outputText[i];

        if (char === ".") {
          // Dot
          await this.playTone(
            this.audioContext,
            frequency,
            currentTime,
            dotDuration / 1000
          );
          currentTime += (dotDuration + gapDuration) / 1000;
        } else if (char === "-") {
          // Dash
          await this.playTone(
            this.audioContext,
            frequency,
            currentTime,
            dashDuration / 1000
          );
          currentTime += (dashDuration + gapDuration) / 1000;
        } else if (char === " ") {
          // Letter gap
          currentTime += letterGapDuration / 1000;
        } else if (char === "/") {
          // Word gap
          currentTime += wordGapDuration / 1000;
        }
      }

      this.audioTimeoutId = setTimeout(() => {
        this.isPlaying = false;
      }, (currentTime - this.audioContext.currentTime) * 1000);
    },

    stopMorseAudio() {
      if (this.audioContext) {
        this.audioContext.close();
        this.audioContext = null;
      }
      if (this.audioTimeoutId) {
        clearTimeout(this.audioTimeoutId);
        this.audioTimeoutId = null;
      }
      this.isPlaying = false;
    },

    playTone(audioContext, frequency, startTime, duration) {
      const oscillator = audioContext.createOscillator();
      const gainNode = audioContext.createGain();

      oscillator.connect(gainNode);
      gainNode.connect(audioContext.destination);

      oscillator.frequency.setValueAtTime(frequency, startTime);
      oscillator.type = "sine";

      gainNode.gain.setValueAtTime(0, startTime);
      gainNode.gain.linearRampToValueAtTime(0.3, startTime + 0.01);
      gainNode.gain.setValueAtTime(0.3, startTime + duration - 0.01);
      gainNode.gain.linearRampToValueAtTime(0, startTime + duration);

      oscillator.start(startTime);
      oscillator.stop(startTime + duration);
    },
  },
};
</script>

<style scoped>
.slide-enter-active, .slide-leave-active {
  transition: grid-template-rows 0.3s ease-in-out, opacity 0.3s ease-in-out;
  grid-template-rows: 1fr;
  opacity: 1;
}
.slide-enter-from, .slide-leave-to {
  grid-template-rows: 0fr;
  opacity: 0;
}

/* For a smoother transition, we can wrap the content and transition its height */
.slide-enter-active, .slide-leave-active {
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  overflow: hidden;
}
.slide-enter-from, .slide-leave-to {
  max-height: 0;
  opacity: 0;
  padding-top: 0;
  padding-bottom: 0;
  margin-top: 0;
  margin-bottom: 0;
}
.slide-enter-to, .slide-leave-from {
  max-height: 1000px; /* A large enough value */
  opacity: 1;
}

/* Add some prose styling for the about section */
.prose {
  color: #d1d5db; /* text-gray-300 */
}
.prose h3 {
  color: #f8fafc; /* text-slate-50 */
}
.prose h4 {
  color: #e2e8f0; /* text-slate-200 */
}
.prose a {
  color: #60a5fa; /* text-blue-400 */
}
.prose a:hover {
  color: #93c5fd; /* text-blue-300 */
}
.prose code {
  color: #fcd34d; /* text-amber-300 */
  font-weight: 600;
}
</style>
