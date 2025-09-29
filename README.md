# Automated-Terminology-Mining-in-Animal-QTL-Research

## 📖 Background  
Thanks to technological advances, animal geneticists have an ever-expanding tool chest with which to study the inheritance of traits in livestock and improve production. With the rise of high-throughput technology, vast amounts of genotype/phenotype data are being rapidly generated.  

Animal QTLdb and other genotype/phenotype databases would greatly benefit from automated and expedited curation tools. While it is well recognized that the adoption of controlled vocabularies and ontologies facilitates data integration and reuse, extracting such terms from scientific texts automatically remains a challenge.  

This project analyzes the terminologies used in a curated corpus and provides insights into the trends of studies.  

---

## Data  
**Note:** The datasets are **not included** in this repository due to restrictions. You will need to provide your own.  

The project expects two files:  

1. **`QTL_text.json`** – contains abstracts of scientific papers.  
   - Fields: `{PMID, Journal, Title, Abstract, Category}`  
   - Only `Abstract` and `Category` are used.  
   - `Category = 1` → animal QTL-related (kept)  
   - `Category = 0` → not related (ignored)  

2. **`Trait_dictionary.txt`** – contains domain-specific trait terms (one per line).  

---

## Features Implemented  

- **Preprocessing**  
  - Sentence segmentation & tokenization (NLTK)  
  - Lowercasing  
  - Removal of stopwords and punctuation  

- **Task 1: Word Cloud Generation**  
  - Frequency-based word cloud  
  - TF–IDF-based word cloud  

- **Task 2: Word2Vec Modeling**  
  - Trained with: `vector_size=100`, `window=5`, `min_count=10`  
  - Extracts 20 most similar words for top 10 TF–IDF terms  

- **Task 3: Phrase Mining with Gensim**  
  - Iterative n-gram model (up to 5-grams) using `Phrases`  
  - Tunable parameters: `min_count`, `threshold`  
  - Generates phrase-level word clouds and Word2Vec models  
  - Normalization applied (underscores → spaces, lowercasing)  
  - Dictionary matching against trait dictionary  

- **Task 4: NP Chunking (Grammar-based)**  
  - RegExp-based grammar:  
    ```
    NP → (JJ)* (NN.*)+
    ```  
  - Extracts noun phrases, normalizes them (lowercasing), and matches with trait dictionary  

---

## Example Results  

### Gensim N-gram Phrase Extraction  

| Parameters (min_count, threshold) | Extracted Phrases | Dictionary Matches |
|-----------------------------------|-----------------:|-------------------:|
| (4, 4.0)                          | 2649             | 106                |
| (3, 3.0)                          | 4013             | 119                |
| (2, 2.0)                          | 7092             | 142 (reported in main section) |

### NP Chunking  

| Method        | Extracted Phrases | Dictionary Matches | Notes                          |
|---------------|-----------------:|-------------------:|--------------------------------|
| NP Chunking   | 26,310           | 229                | Broad coverage, high redundancy |

---

## Output Files  

When executed, the script creates an **`outputs/`** directory with the following results:  

- **Word Clouds**  
  - `wordcloud_frequency.png` – unigram frequency word cloud  
  - `wordcloud_tfidf.png` – unigram TF–IDF word cloud  
  - `phrase_wordcloud_frequency.png` – phrase-level frequency word cloud  
  - `phrase_wordcloud_tfidf.png` – phrase-level TF–IDF word cloud  

- **Word2Vec Similarity**  
  - `similar_words_raw.txt` – similar words for top TF–IDF terms (unigrams)  
  - `similar_words_phrases.txt` – similar words for top TF–IDF terms (phrases)  

- **Dictionary Matching**  
  - `trait_dictionary_match_gensim.txt` – extracted phrases vs. trait dictionary (Gensim)  
  - `trait_dictionary_match_npchunk.txt` – extracted phrases vs. trait dictionary (NP Chunking)  

---

## Dependencies  

Install all required libraries with:  

```bash
pip install gensim==4.3.3 wordcloud==1.9.3 nltk==3.9.1 scikit-learn==1.5.2 matplotlib==3.9.2 numpy==1.26.4 scipy==1.13.1
