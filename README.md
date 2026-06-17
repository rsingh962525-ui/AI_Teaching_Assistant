# SmartBoard Voice-Enabled AI Teaching Assistant

A modern, voice-activated smartboard prototype built for government school classrooms. It serves as an interactive co-pilot for teachers, enabling continuous voice-controlled lessons, real-time Hinglish concept explanations, and voice-triggered quiz evaluations that are highly readable from the back of a classroom.

---

## ✨ Key Features

- **Classroom-Optimized Layout**: A clean, high-contrast, dark-mode design using ultra-large typography specifically tailored for visibility from the back rows of a classroom.
- **Continuous Speech Recognition**: Leverages the browser's native `SpeechRecognition` API (Web Speech API) with custom mounting lifecycle controls to prevent closure staleness and audio loop restarts.
- **Real-Time Transcript Overlay**: A glowing live transcript feed presenting the spoken lecture text at the bottom of the screen.
- **Voice-Triggered Quizzing**: Automatically detects trigger words like `"quiz"` or `"question"` in the spoken transcript and sends a flag to the backend to dynamically generate a multiple-choice question on the concept.
- **Interactive Option Cards**: Dynamic rendering of AI-generated options in 4 distinct, colorful cards (A, B, C, D). Includes instant grading highlights (green for correct, red for incorrect) and deep Hinglish pedagogical feedback.
- **SpeechSynthesis Auto Read-Aloud**: Uses browser-native text-to-speech to speak the AI's explanation or dynamic quiz question out loud automatically upon arrival on the smartboard.
- **Gemini 2.5 Flash Integration**: Connected to Google AI Studio's newest stable model using JSON schema outputs to retrieve natural Hinglish responses.
- **Robust Offline Fallback**: Features a local concept dictionary for core topics (gravity, photosynthesis, electricity, etc.) to gracefully simulate replies when offline or when API keys are missing.

---

## 🛠️ Architecture

### 1. API Route (`src/app/api/chat/route.ts`)
The server-side route handles POST requests:
- Detects the `quizMode` flag from the client.
- Passes a customized `QUIZ_SYSTEM_PROMPT` to Gemini if active, prompting for a structured JSON matching the quiz question schema:
  ```json
  {
    "question": "Clear bold question text in Hinglish",
    "options": {
      "A": "Option A",
      "B": "Option B",
      "C": "Option C",
      "D": "Option D"
    },
    "correct": "A",
    "explanationHinglish": "Pedagogical explanation in simple Hinglish",
    "explanationEnglish": "Pedagogical explanation in English"
  }
  ```
- Uses `responseMimeType: "application/json"` to enforce structured JSON parsing.
- Uses `gemini-2.5-flash` for high-speed, cost-efficient, stable inference.
- Falls back to `getFallbackQuizResponse` or `getFallbackResponse` if API credentials fail.

### 2. Frontend Interface (`src/app/page.tsx`)
- Coordinates Web Speech API lifecycles.
- Dynamically parses markdown (e.g. `**` bold indicators, `*` list bullets) using a custom client-side regular expression parser to avoid third-party library bloat.
- Renders the interactive quiz interface with IIFE wrappers supporting local static quizzes and dynamic AI-generated quiz questions.

---

## 🚀 Setup & Local Development

### 1. Install Dependencies
Run from the workspace directory:
```bash
npm install
```

### 2. Configure Environment Variables
Create a `.env.local` file in the root of the project:
```env
GEMINI_API_KEY=your_gemini_api_key_here
```
*(Supports standard keys starting with `AQ.` or `AIza`)*

### 3. Run the Dev Server
Launch the local server:
```bash
npm run dev
```
Open [http://localhost:3000](http://localhost:3000) to view the Smartboard interface.

---

## 🧪 Voice Commands Cheat Sheet

Speak these phrases clearly while the microphone is enabled:
- **Explanations**:
  - *"Explain Photosynthesis"*
  - *"What is Gravity?"*
  - *"Define Friction"*
- **Quiz Controls**:
  - *"Explain Gravity Question"* (Generates a dynamic live quiz about gravity)
  - *"Photosynthesis Quiz"* (Generates a dynamic live quiz about photosynthesis)
  - *"Start Quiz"* (Starts the local pre-defined interactive quiz)
  - *"Option A"* / *"Option B"* / *"Option C"* / *"Option D"* (Selects option during quiz)
- **Smartboard Resets**:
  - *"Go Back"* / *"Home"* (Resets the screen to the landing state)
  - *"Stop Listening"* / *"Turn off mic"* (Deactivates recording stream)
