# spanishOCR

An AI-driven document reading and correction pipeline designed to transcribe and reconstruct degraded, 17th-century Spanish historical texts. This pipeline solves common VLM token-exhaustion and hallucination issues by utilizing a deterministic, multi-level layout slicing engine.

## Pipeline Architecture
<img width="1350" height="212" alt="updated_pipeline_workflow" src="https://github.com/user-attachments/assets/6a19b873-c52e-4a17-9835-8c16ae093143" />

This project integrates AI at all stages of document analysis:

* **Stage 1: Layout Analysis:** Utilizes OpenCV with smoothed projection profiles (Gaussian blurring) to detect book-spines, gutters, and paragraphs. It dynamically slices dense historical pages into manageable chunks to prevent token-exhaustion.
* **Stage 2: Visual Perception (VLM):** Uses Microsoft's `Florence-2-large` to perform zero-shot raw OCR. It acts as the "eyes" of the system, extracting raw archaic character orthography directly from image slices.
* **Stage 3: Semantic Reconstruction (LLM):** Passes the raw output to `Gemini 1.5 Flash` acting as a historical linguistic agent. Guided by a deterministic system instruction (temperature 0.0), it applies 17th-century orthographic rules (e.g., interchangeable u/v and f/s, mapping the cedilla ç to z) to correct faded ink and resolve abbreviations without creative hallucination.

<!-- 
Add your pipeline diagram image here by saving the image we generated earlier into your GitHub repo and uncommenting the line below:
![Pipeline Flowchart](updated_pipeline_workflow.png) 
-->

## Evaluation Results

The pipeline was graded against professor-verified Ground Truth documents utilizing the `jiwer` evaluation metric library.

| DATA | CER | WER | MER | WIL | Grnd Truth | AI Output |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Buendia** | 0.0556 | 0.1513 | 0.1474 | 0.2504 | 608 | 621 |
| **Covarrubias** | 0.0615 | 0.1483 | 0.1450 | 0.2363 | 2037 | 2039 |
| **Guardiola** | 0.0303 | 0.0975 | 0.0966 | 0.1689 | 605 | 606 |
| **PORCONES 1628** | 0.1129 | 0.2892 | 0.3055 | 0.4876 | 1474 | 1421 |
| **PORCONES 1646** | 0.0796 | 0.2036 | 0.2370 | 0.3825 | 1326 | 1321 |
| **PORCONES 1650** | 0.3416 | 0.5582 | 0.4375 | 0.5786 | 953 | 1165 |

> **Remarks:** 
> * Evaluated the document `PORCONES1628` only on 3 pages, exempted P4 to remove confusion.
> * Some lines of the document `PORCONES1650` are bended, meaning the model is unable to draw a perfect layout among the lines, which resulted in higher error rates.
<img width="1000" height="1100" alt="layout" src="https://github.com/user-attachments/assets/7b37546f-947c-4d15-ae5a-6d044f0d93c3" />

## Tech Stack

* **Vision Model:** [Microsoft Florence-2-large](https://huggingface.co/microsoft/Florence-2-large)
* **Language Model:** Google Gemini 1.5 Flash
* **Preprocessing:** OpenCV, Numpy, Pdf2Image
* **Evaluation:** Jiwer (CER, WER, MER, WIL)
* **Visualization:** Graphviz, Matplotlib
* **Backend:** Python / PyTorch

## Setup & Installation

1. Clone the repository
2. Install the required system and Python dependencies:
```bash
apt-get update
apt-get install -y poppler-utils
pip install transformers==4.49.0 jiwer opencv-python-headless google-generativeai pdf2image pydot graphviz
