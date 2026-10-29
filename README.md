# Inelastic Search

A web search engine written from scratch in Python: HTML cleaning, a stemmed inverted index, Boolean retrieval, and TF-IDF ranking blended with PageRank, behind a command-line and a Tkinter interface. No Lucene, Elasticsearch or Whoosh, just the data structures.

It indexes UCI's ICS web crawl (the `DEV` corpus of JSON page dumps).

## Pipeline

```
 page JSON ─▶ extract_text ─▶ tokenizer ─▶ build_index ─▶ index/inverted_index.json
 (url, html)   strip scripts,    lowercase,    term → postings          + doc lookup
               hidden elements,  Porter stem   (doc ids, weights)       + PageRank scores
               footers; pull
               title/h1-h3/b/strong
                                                            │
 query ─▶ tokenizer ─▶ Boolean AND (smallest postings first) ─▶ TF-IDF + scaled PageRank ─▶ top 5
```

### Cleaning what a user actually sees

`extract_text` drops `<script>`, `<style>` and `<noscript>`, anything hidden with inline CSS (`display:none`, `visibility:hidden`, `opacity:0`), `aria-hidden` elements, common hidden classes, and footers. Text from `<title>`, `<h1>`-`<h3>`, `<b>` and `<strong>` is kept separately as "important" text.

### Field-weighted index

Tokens are lowercased, stripped of punctuation and Porter-stemmed with NLTK. Each posting stores a document id and a weight, and a term that appears in an important field adds 2 to its weight, so a match in a page title outranks a match buried in body text.

### Retrieval

Queries go through the same tokenizer, then a Boolean AND intersects the postings lists, starting with the smallest so the working set shrinks as fast as possible, and stops early once it has enough candidates.

### Ranking

Each candidate gets a smoothed TF-IDF score (`idf = log((N+1)/(df+1)) + 1`). PageRank is computed by power iteration (damping 0.85, 20 iterations, dangling nodes spread uniformly) and rescaled into the TF-IDF range before the two are added, so neither signal swamps the other.

### Summaries

`summarizer.py` wraps Hugging Face's `facebook/bart-large-cnn` to produce a two-sentence abstractive summary per result. It's off by default because running BART over a whole crawl at index time is slow; uncomment the calls in `build_index.py` and `search.py` to turn it on.

## Run it

```bash
pip install beautifulsoup4 nltk numpy        # + transformers torch for summaries
# put the corpus at data/developer/DEV/<domain>/<page>.json
python src/main.py
```

The first run offers to build the index, then asks for CLI or GUI. The GUI shows each query's execution time in milliseconds.

## Code map

| File | Role |
|---|---|
| `src/extract_text.py` | HTML to visible text plus important-field text |
| `src/tokenizer.py` | Normalization and Porter stemming |
| `src/read_json_files.py` | Walks the corpus and tokenizes every page |
| `src/build_index.py` | Builds and saves the inverted index |
| `src/query_processor.py` | Boolean AND retrieval |
| `src/scoring.py` | TF-IDF, PageRank, score blending |
| `src/search.py` | Query to ranked top-5 |
| `src/summarizer.py` | BART summarization |
| `src/main.py` | Index-or-load, CLI and Tkinter front ends |

## Next

- Extract outgoing links from the HTML so PageRank runs on the real link graph
- Write the index in partial segments and merge them, so the corpus no longer has to fit in memory
- Store true per-document term counts for TF
- Sparse matrices for PageRank at full-crawl scale
