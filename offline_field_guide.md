# Feature Overview
## Problem Statement
I often I have found myself in the middle of my property working on something, but I need a guide on how to construct something, but there is no cell reception and a youtube video takes way too lon[...]

---
It would be nice if I could store some PDFs on the UTV manager, be able to search through and see pictures or have a properly trained AI to walk me through the steps or answer questions offline whi[...]

## Stuff to Research Later
- Are there existing open source farm and homestead AI or knowledgebases I can use
- What would it take to train a new model if I needed one?
- PDF reader for Raspberry Pi
- Would a 128gb SD be fine for what I need?
- Confirm 8gb of RAM should be adequate. 

## Offline AI (Field Guide) — Build plan

Goal
- Provide an offline, on-device "field guide" assistant that:
  - Serves searchable PDFs, pictures and schematics.
  - Answers how-to questions and walks a user through step-by-step procedures for small-scale farming (orchard, vineyard, small livestock, light construction).
  - Works reliably without internet (fully local retrieval + local model or lightweight local synthesis).

Design approach (tiers)
- Tier A — Field Guide Lite (most feasible on Adafruit / Raspberry Pi parts)
  - Primary capability: offline PDF & media viewer + local semantic search (fast retrieval of passages and images) and short-answer QA using a lightweight local model or deterministic templates. Good for quick lookups and checklists.
  - Hardware target: Raspberry Pi 4 (8 GB), 128GB microSD (or USB SSD for larger archives), touchscreen or HDMI display, portable power (LiPo battery pack).
- Tier B — Field Guide Pro (stronger local LLM + better on-device generation)
  - Primary capability: full retrieval-augmented generation (RAG) with a quantized LLM that can produce step-by-step guidance conversationally offline.
  - Hardware target: an SBC with accelerator (e.g., a small x86/NVIDIA device or an ARM device + Coral/TPU accelerator). More power, larger storage.

Parts (Adafruit-focused shopping list)
- Core compute
  - Raspberry Pi 4 Model B — 8 GB RAM (Adafruit carries Pi boards and kits).
  - microSD card — 128 GB (use high-quality brand; Adafruit sells SanDisk/Kingston cards).
  - Optional: USB 3.0 NVMe or SATA SSD + USB 3.0 adapter (for large local content).
- Power & battery (portable)
  - USB-C power supply 5.1V 3A (for Pi 4) and/or Adafruit PowerBoost / LiPo battery + USB-C boost if you need hot-swappable portable operation.
  - In-car or 12 V to 5 V converter if you want to run from UTV battery.
- Display & input
  - 7" HDMI display kit (or PiTFT 2.8" / 3.5" touchscreen for compact builds) — Adafruit sells several Pi displays.
  - Compact Bluetooth or USB keyboard, or a rugged touch interface.
- Acceleration (optional for Tier B)
  - Google Coral USB Accelerator (Edge TPU) — Adafruit lists Coral devices; good for ONNX/TFLite inference acceleration of some models.
  - Note: Coral accelerates specific model types (TFLite); not a general GPU for large LLMs.
- Camera / sensors (optional)
  - Raspberry Pi camera module (Adafruit stock) for documenting work or OCR of printed content.
- Enclosure & cooling
  - Heatsink/fan-enabled Pi case (Adafruit sells Pi cases with cooling).
  - Ruggedized box, gasketed case, or a waterproof case for field use.
- Connectivity for local networks (optional)
  - USB Wi-Fi dongle (if you want a local hotspot) or use Pi’s onboard Wi-Fi to create a local AP.

Software stack (open-source components)
- OS
  - 64-bit Raspberry Pi OS or Ubuntu Server (64-bit) for Pi 4; keep kernel and firmware updated.
- PDF & media
  - PDF viewer: qpdf/poppler-utils/pdftotext for extraction; a GUI viewer for display (e.g., Firefox or mupdf on Pi).
  - OCR: Tesseract for scanned images inside PDFs.
- Ingestion & indexing
  - Text extraction: pypdf, pdfplumber, or pdftotext + OCR fallback.
  - Chunking: split long documents into ~500–1000 token chunks (overlap 50–100 tokens).
  - Embeddings: use a small local embedding model (sentence-transformers small models) or ggml/quantized embedding models (ARM-compatible).
  - Vector DB: Chroma, FAISS, or simple disk-based ANN (Annoy). Chroma and FAISS both run locally; choose small-memory index on Pi.
- QA / LLM
  - Lightweight/edge LLMs:
    - llama.cpp / ggml with quantized ggml models (suitable on devices with reduced RAM; many community quantized models exist).
    - GPT4All or other local-LLM distributions that provide ARM builds.
    - For Tier A: prefer deterministic answer templates + retrieval and small local generator for short answers.
  - RAG pipeline: nearest-neighbor search → assemble context → prompt to local model (or template-based summarizer).
  - Web UI / API: text-generation-webui, FastAPI, or a simple Flask app serving a local web UI accessible via the Pi's Wi-Fi.
- Auxiliary
  - Front-end: lightweight React/Electron or plain HTML/CSS served from Pi for touchscreen UI.
  - Search UI: simple keyword + semantic search with adjustable result count.
  - Backup & sync: rsync or USB backup routine for moving new PDFs between desktop and field unit.

Data & content pipeline
- Prepare content offline before deployment:
  - Convert PDFs to text; store images separately and reference in metadata.
  - Create metadata (title, tags, source, date, locality, relevant crop/animal/technique).
  - Generate embeddings for all chunks and save the vector index on-device.
- Update workflow:
  - A desktop tool (or script) that ingests new PDFs, creates embeddings and index, then transfers index + raw PDFs to the Pi via USB or local network.
  - For field updates without internet: copy via microSD/USB.

Deployment & UX considerations
- Boot-to-ready: configure Pi to run the field-guide app at startup; touch-first UI with a “Search” box and “Browse PDFs” mode.
- Minimal latency: keep top-k retrieval low (k=3–8) and avoid very large prompts on-device.
- Conversation safety: show provenance (which PDF+page produced the passage) and a confidence indicator.
- Offline fallback: if generation is too slow on-device, fall back to returning top relevant passages verbatim, with simple step-by-step extraction templates.

Step-by-step build plan
1. Procurement
   - Order Pi 4 (8GB), microSD (128GB), display, keyboard, case, power supply, and optional Coral USB Accelerator + SSD if choosing Tier B.
2. OS and base setup
   - Flash 64-bit OS, create user account, enable SSH, set timezone, and configure swap (careful with swap on SD).
   - Install system packages (build-essential, python3, pip, poppler-utils, tesseract).
3. Ingest pipeline locally on desktop
   - Write/assemble scripts to convert PDFs → text → chunks → embeddings.
   - Test embedding generation and vector index creation on desktop for speed.
4. Index transfer & local test
   - Copy PDFs + index to Pi/SSD and run the search-only UI to confirm retrieval accuracy.
5. Lightweight QA integration
   - Integrate a local small LLM or template-based answer engine; test RAG latency and answer quality.
   - If responses are too slow, reduce model size or switch to returning snippets with instructions.
6. UI & UX polish
   - Make quick-access menus: "Guides", "Search", "Ask", "Camera", "Save notes".
   - Add provenance links (PDF name + page numbers), picture preview thumbnails.
7. Field test
   - Test in conditions: cold/hot, no cell, running from UTV battery, offline updates from USB.
8. Optimize & document
   - Add quick start, update script, and instructions for adding new PDFs and regenerating the index.

Model training, fine-tuning & licensing notes
- You can fine-tune on domain-specific instructions, but this requires more compute. Consider:
  - Fine-tuning small models (7B-class) using a desktop or cloud GPU. Then quantize for local use.
  - Using prompt engineering and curated instruction templates may be sufficient for many tasks.
- Licensing: only use model weights that permit local use (check Llama2, Mistral, and other weights for terms). Avoid redistributing licensed weights with your device without compliance.

Costs (rough estimates)
- Tier A (Pi-based, display, microSD, power): approximately $150–400 depending on display, SSD and battery choices.
- Tier B (accelerator, SSD, bigger compute): $400–1,500+ depending on accelerator, SSD, or moving to a Jetson/NUC-style device.

Tradeoffs & risks
- Pi 4 (8 GB) is fine for PDF search + small inference; real conversational generation with 7B+ models will be slow or impossible without quantization/acceleration.
- Coral Edge TPU accelerates TFLite models but cannot run arbitrary PyTorch GGML models.
- Battery life and heat management are important for field use—plan cooling and a robust power path.
- Embeddings and indexes can grow; prefer SSD for long-term storage over microSD.

Next steps / recommended immediate actions
1. Choose Tier (A or B) depending on desired on-device generation ability.
2. Order a Pi 4 8GB kit, 128GB microSD, small touchscreen (Adafruit Pi display), power supply and a rugged case as first build.
3. I can provide:
   - The exact Adafruit product links and SKUs for the parts above (if you want me to look them up).
   - A starter script that ingests PDFs, chunks text, builds embeddings (with Chroma/FAISS), and runs a minimal search web UI for Pi.
   - A ready-to-run image/config for the Pi with the app installed (requires testing).
