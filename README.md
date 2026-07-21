# Inelastic Search

> **Spacetime-powered web crawler · AI-Driven Summarization · High-Performance Inverted Index**

Inelastic Search is a high-performance, full-stack information retrieval system built from scratch in Python. It is designed to index and search large-scale document corpora with sub-300ms latency. 

What sets Inelastic Search apart is its integration of **Generative AI** to provide content-aware summaries, significantly enhancing the user's ability to gauge result relevance instantly.

---

##  Key Features

###  Search Core
- **Scalable Indexing**: Implements a memory-bounded partial indexing strategy with K-way merging to handle datasets larger than RAM.
- **Ranked Retrieval**: Utilizes a hybrid scoring algorithm combining **TF-IDF** (Term Frequency-Inverse Document Frequency) and **PageRank** for high-precision ranking.
- **Boolean Queries**: Supports complex query parsing including `AND`, `OR`, and `NOT` operations.

###  AI Summarizer
- **Generative Summaries**: Integrated with the **BART-Large-CNN** Transformer model (via Hugging Face) to generate abstractive summaries of search results.
- **Concise Insights**: Automatically condenses lengthy documents into 2-sentence "at-a-glance" overviews.

###  versatile Interfaces
- **GUI Mode**: A clean, responsive Tkinter graphical interface for visual interaction and performance metrics.
- **CLI Mode**: A lightweight command-line tools for rapid queries and debugging.

---

##  Architecture

1.  **Crawler**: Fetches web content, parsing HTML and text.
2.  **Indexer**: Tokenizes content, builds inverted indexes, and writes optimized partial segments to disk.
3.  **Link Analysis**: Computes PageRank scores to determine domain authority and page importance.
4.  **Query Engine**: 
    *   Parses user input.
    *   Retrieves posting lists.
    *   Computes relevance scores.
    *   **AI Layer**: Passes top results through the Summarization Pipeline.

---

##  Getting Started

### Prerequisites
*   Python 3.10+
*   `pip`

### Installation

1.  **Clone the repository**
    ```bash
    git clone https://github.com/suyashj1231/Inelastic-Search.git
    cd Inelastic-Search
    ```

2.  **Install Dependencies**
    ```bash
    pip install transformers torch nltk
    # Note: Torch is required for the transformers pipeline
    ```

### Usage

Run the main application entry point. You will be prompted to build the index (if not already present) and choose your interface.

```bash
python src/main.py
```

*   **Option 1**: Command Line Interface (fast, text-only).
*   **Option 2**: Graphical User Interface (visual, includes timing stats).

---

##  Project Highlights

*   **Efficiency**: Achieves fast query times on 50k+ documents.
*   **Intelligence**: Moves beyond keyword matching by adding semantic understanding through summarization.
*   **Modularity**: Clean separation of concerns between crawling, indexing, ranking, and UI layers.

---

*Created by Suyash. This project serves as a demonstration of advanced Information Retrieval combined with modern Web Search architectures.*
