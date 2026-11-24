# Complement: AI-Powered Desktop Assistant & Next-Step Predictor

Hello! Thanks for checking out my project!

I built Complement in Rust, Tauri, and PyTorch to explore desktop systems programming and challenge myself to take a traditional desktop launcher (like Spotlight or Alfred) and replace static search with a lightweight, embedded neural network that learns your workflow habits and predicts your next application or action before you even type a character.

$$\mathbf{x} = \begin{bmatrix} t_{\text{hour}} \\ t_{\text{day}} \\ \mathbb{I}_{\text{weekend}} \\ \Delta t_{\text{recency}} \\ f_{\text{freq}} \\ s_{\text{recent}} \\ c_{\text{window}} \\ c_{\text{search}} \end{bmatrix} \in [0, 1]^8 \quad \xrightarrow{\quad \text{ONNX Runtime} \quad} \quad \hat{\mathbf{y}} = \operatorname{softmax}(W_4 \cdot \operatorname{ReLU}(W_3 \dots W_1 \mathbf{x}))$$

In this engine, every application launch, search query, and foreground window transition is captured as a local temporal event in an embedded SQLite database configured in Write-Ahead Logging (WAL) mode. Complement transforms these signals into an 8-dimensional context feature vector and feeds them through an embedded ONNX neural network to predict user intent in **sub-10ms** with a **<50MB memory footprint**—running **100% locally with zero cloud dependencies or telemetry**.

Built from scratch using Rust, Tauri 2.0, PyTorch, ONNX Runtime, and SQLite.

---

## Architecture & Deep Dive

I wrote a comprehensive architecture guide with Mermaid diagrams, dataflow pipelines, benchmark breakdowns, and subsystem analyses covering the 8D context feature extraction, ONNX runtime integration, SQLite WAL tuning, cross-platform app indexing, and transparent click-through window physics.

**Check it out if you're interested! [ARCHITECTURE.md](ARCHITECTURE.md)**

---

## Performance Highlights

| Metric | Complement (Rust + Tauri) | Electron Launchers | Traditional OS Search |
| :--- | :--- | :--- | :--- |
| **Search Response Time** | **<5 ms** across 10k+ items | 45 ms - 120 ms | 15 ms - 50 ms |
| **ML Inference Latency** | **<10 ms** (Embedded ONNX) | N/A (Cloud-based 150ms+) | N/A (Static search) |
| **Cold Start to Render** | **<200 ms** to interactive HUD | 800 ms - 2,500 ms | Instant (Background service) |
| **Idle Memory Footprint** | **<50 MB** baseline | 350 MB - 750 MB | ~80 MB |
| **Background CPU Usage** | **<0.2%** utilization | 1.5% - 4.0% | <0.5% |
| **Privacy & Telemetry** | **100% Local (Zero Network)** | Cloud-dependent features | Cloud/Bing telemetry |

---

## Features

| Feature | Implementation | Performance & Notes |
| :--- | :--- | :--- |
| **Context Predictions** | 8D Feature Extractor + ONNX Runtime MLP | Sub-10ms local inference with explainable reasoning |
| **Instant Fuzzy Search** | Modified Smith-Waterman via `SkimMatcherV2` | Sub-5ms fuzzy search across thousands of system apps |
| **Clipboard History** | Background thread + SQLite WAL ring buffer | 500ms poller with duplicate suppression & source tracking |
| **Snippet Expansion** | Keyword trigger (`:keyword`) via SQLite | Sub-millisecond template expansion to clipboard |
| **Math Calculator** | AST expression parsing via `meval` | Instant inline evaluation; copies to clipboard on Enter |
| **Direct Web Search** | URL-encoded engine launcher (`g <query>`) | Launches directly in default browser with zero latency |
| **Transparent Overlay** | Borderless click-through Tauri window | Hardware-accelerated CSS glassmorphism & dynamic resizing |

---

## Quickstart

### Prerequisites
* **Rust**: Latest stable toolchain (`rustup`)
* **Node.js**: v18+ and `npm`
* **Python**: 3.9+ (optional, only required for retraining the ML model)

```bash
# 1. Clone the repository
git clone https://github.com/GadirajuHarsha/next-step-assistant.git
cd next-step-assistant/complement

# 2. Install frontend dependencies
npm install

# 3. (Optional) Install Python dependencies for ML training
pip install -r ml_training/requirements.txt
```

### Running the Application

To run Complement in development mode with hot-reloading for both Rust and TypeScript:

```bash
npm run tauri dev
```

To build a standalone production executable:

```bash
npm run tauri build
```

### Training the Machine Learning Model

Complement ships with pre-trained ONNX model artifacts in `complement/models/`. If you want to retrain the neural network on synthetic or exported usage data:

```bash
cd complement/ml_training
python train_model.py
```

This script:
1. Generates 15,000 synthetic behavioral context samples simulating real-world work habits.
2. Trains a 4-layer perceptron in PyTorch with dropout regularization.
3. Automatically exports the optimized model to `models/complement_recommendation_model.onnx` with dynamic batch axes and saves class mappings to `models/app_labels.txt`.

---

## Command System & Usage Guide

Once launched, Complement operates as an unobtrusive command HUD:

| Trigger / Command | Action | Example |
| :--- | :--- | :--- |
| `Ctrl+Alt+Space` | Global hotkey to toggle Complement from anywhere | Press anywhere to summon HUD |
| *(Empty Query)* | Surfaces context-aware AI recommendations & greeting | Opens with top 3 anticipated apps |
| `<app name>` | Real-time fuzzy search across system applications | Type `code`, `term`, `calc` |
| `ml` or `ai` | Displays top ONNX neural network predictions & confidence | Type `ml` to view 5 scored predictions |
| `c` or `clip` | Opens recent clipboard history ring buffer | Type `c` to browse and restore clips |
| `:keyword` | Expands saved text snippet templates | Type `:email` to copy saved template |
| `<math expression>` | Evaluates mathematical formulas; Enter copies result | Type `(120 * 4) / 1.5` |
| `g <search query>` | Executes a direct Google web search in default browser | Type `g rust tauri 2.0` |
| `exit` | Exits Complement with a smooth scale-fade animation | Type `exit` and press Enter |

---

## Thanks for Visiting!
