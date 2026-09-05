# Map-Reduce Document Summarization

A Python project that summarizes large PDF documents using the **Map-Reduce** pattern with the **Groq API**.

Large documents are split into manageable chunks, each chunk is summarized independently (**Map**), and the chunk summaries are then combined into a single cohesive summary (**Reduce**) — avoiding the need to pass an entire document into the LLM at once.

---

## How It Works

```mermaid
flowchart TD
    A[ PDF] --> B[Load & Extract Text]
    B --> C[Split into Chunks]
    C --> D[ MAP: Summarize Each Chunk]
    D --> E[ REDUCE: Combine Chunk Summaries]
    E --> F[ Final Summary]

    style A stroke:#1e88e5
    style F stroke:#43a047
    style D stroke:#fb8c00
    style E stroke:#d81b60
```

### Pipeline in Detail

```mermaid
sequenceDiagram
    participant U as User
    participant L as loader.py
    participant C as chunks.py
    participant S as summarizer.py (Map)
    participant R as summarizer.py (Reduce)
    participant O as output/

    U->>L: Run main.py
    L->>L: Extract text from PDF
    L->>C: Pass raw text
    C->>C: Split into chunks (with overlap)
    C->>C: Attach metadata (chunk_id, page_number, source)
    C->>S: Send chunks
    loop For each chunk
        S->>S: Summarize chunk via Groq API
    end
    S->>O: Save chunk_summaries.json
    S->>R: Send chunk summaries
    R->>R: Combine into master summary
    R->>O: Save final_summary.md
    O-->>U: Pipeline complete
```

---

## Features

- 📄 PDF text extraction
- ✂️ Recursive text chunking with overlap
- 🧠 Chunk-level LLM summarization
- 🏷️ Metadata preservation (`chunk_id`, `page_number`, `source`)
- 🔀 Map-Reduce summarization
- 📦 JSON output for chunk summaries
- 📝 Markdown output for the final summary
- 📊 Basic programmatic evaluation

---

## Project Structure

```mermaid
flowchart LR
    subgraph Root["Map-Reduce-Document-Summarization/"]
        direction TB
        subgraph data["data/"]
            pdf["source.pdf"]
        end
        subgraph output["output/"]
            json["chunk_summaries.json"]
            md["final_summary.md"]
        end
        loader["loader.py"]
        chunks["chunks.py"]
        summarizer["summarizer.py"]
        main["main.py"]
        eval["eval.py"]
        gitignore[".gitignore"]
        readme["README.md"]
    end
```

```text
Map-Reduce-Document-Summarization/
│
├── data/
│   └── source.pdf
├── output/
│   ├── chunk_summaries.json
│   └── final_summary.md
│
├── loader.py
├── chunks.py
├── summarizer.py
├── main.py
├── eval.py
├── .gitignore
└── README.md
```

---

## Files

| File | Responsibility |
|---|---|
| `loader.py` | Loads and extracts text from the PDF |
| `chunks.py` | Splits text into chunks and preserves metadata |
| `summarizer.py` | Implements the Map and Reduce summarization steps |
| `main.py` | Runs the complete pipeline |
| `eval.py` | Evaluates chunk and final-summary statistics |

---

## Setup

Clone the repository and install dependencies:

```bash
git clone https://github.com/PUNITH-V/Map-Reduce-Document-Summarization.git
cd Map-Reduce-Document-Summarization
pip install groq python-dotenv langchain-text-splitters
```

Create a `.env` file:

```env
GROQ_API_KEY=your_api_key_here
```

Place your PDF at:

```text
data/source.pdf
```

---

## Run

Run the complete pipeline:

```bash
python main.py
```

Evaluate the results:

```bash
python eval.py
```

---

## Output

The pipeline generates:

```text
output/
├── chunk_summaries.json
└── final_summary.md
```

- **`chunk_summaries.json`** — contains the original chunks, summaries, and metadata.
- **`final_summary.md`** — contains the final master summary produced by the Reduce step.

---

## Evaluation

`eval.py` reports:

- Total page count
- Total chunk count
- Empty chunk summaries
- Longest map-summary word count
- Final-summary word count
- Whether the final summary is under 500 words

---

## Tech Stack

- Python
- Groq API
- LangChain Text Splitters
- python-dotenv

---
