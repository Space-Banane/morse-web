<template>
  <div id="app">
    <MorseCodeApp />
    
  </div>
</template>

<script>
import MorseCodeApp from './components/MorseCodeApp.vue'

export default {
  name: 'App',
  components: {
    MorseCodeApp
  }
}
</script>

<style>
html, body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  background-color: #0f172a; /* slate-900 */
  color: #cbd5e1; /* slate-300 */
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol';
}

#app {
  min-height: 100vh;
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.explanation-container {
  max-width: 800px;
  padding: 2rem;
  line-height: 1.6;
}

.explanation-container h2 {
  color: #f1f5f9; /* slate-100 */
  border-bottom: 1px solid #334155; /* slate-700 */
  padding-bottom: 0.5rem;
  margin-top: 2rem;
}

.explanation-container h3 {
  color: #e2e8f0; /* slate-200 */
  margin-top: 1.5rem;
}

.explanation-container p,
.explanation-container li {
  color: #94a3b8; /* slate-400 */
}

.explanation-container ul,
.explanation-container ol {
  padding-left: 1.5rem;
}

.explanation-container code {
  background-color: #1e293b; /* slate-800 */
  color: #f8fafc; /* slate-50 */
  padding: 0.2em 0.4em;
  border-radius: 3px;
  font-family: 'Courier New', Courier, monospace;
}

.explanation-container pre {
  background-color: #1e293b; /* slate-800 */
  padding: 1rem;
  border-radius: 5px;
  overflow-x: auto;
}

.explanation-container pre code {
  padding: 0;
  background-color: transparent;
}
</style>