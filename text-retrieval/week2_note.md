

# Vector space model -improved instantiation

- Improved vector placement: term frequency vector
- add inverse document frequency (IDF)
  - $q = (x_1, … x_N)$, $x_i$ = count of word $W_i$ in query
  - $d=(y_1,…,y_N)$, $y_i = c(W_i, d) * IDF(W_i)$
- IDF weighting: penalizing popular terms
  - $IDF(W) = log[(M+1)/k]$
  - M: total number of docs in collection
  - k: total number of docs containing W (Doc Frequency)
- Improved VSM
  - dimensoion = word
  - vector = tf-idf weight vector
  - similarity = doc product
  - working better than the simplest VSM
  - still having problems

# TF Transformation

## ranking function with TF-IDF weighting

$$
f(q,d) = \sum_{i=1}^N x_i y_i = \sum_{w \in q \cap d} c(w,q)c(w,d)log \frac{M+1}{df(w)}
$$

- $w \in q \cap d$ : all matched query words in d
- $M$: total # of docs in collection
- $df(w)$:doc frequency

## TF transformation: c(w, d) -> TF(w,d )

- Term Frequency Weight
- BM25 tranformation
  - $x= c(w,d),  y = TF(w, d), y = \frac {(k+1)x} {x+k} $ 
  - upper bound of y is k+1
  - if $ k = 0 $, then it is 0-1 term frequency weight
  - if $k = \infty$, then it is linear term frequency weight

## Summary

- sublinear TF transformation is needed to

  - capture the intuition of "diminishing return" from higher TF
  - avoid dominance by one single term over all others

- BM25 transformation

  - has an upper bound
  - is robust and effective

- ranking function with BM25 TF (k >= 0)

  - $$
    f(q,d) = \sum_{i=1}^N x_i y_i = \sum_{w \in q \cap d} c(w,q) \frac {(k+1)c(w,d)}{c(w,d)+k} log \frac {M+1}{df(w)}
    $$


# Doc Length Normalization

## Document Length Normalization

- penalize a log doc with a doc length normalizer
  - long doc has a better chance to match any query
  - need to avoid over-penalization
- a document is long because
  - it uses more words -> more penalization
  - it has more contents -> less penalization
- pivoted length normalizer: average doc length as "pivot"
  - normalizer = 1 if |d| = average doc length (avdl)

## Pivoted length normalization

- $ normalizer = 1 - b + b \frac {|d|}{avdl}, b \in [0,1] $

## State of art VSM  Ranking Functions

* pivoted length normalization VSM [Singhal et al 96]

$$
f(q, d) = \sum_{w \in q \cap d} c(w,q) \frac {ln[1+ln[1+c(w,d)]]} {1-b+b \frac {|d|}{avdl}} log \frac {M+1}{df(w)}
$$



* BM25/Okapi [Robertson & Walker 94]

$$
f(q,d) = \sum_{w \in q \cap d} c(w,q) \frac {(k+1)c(w,d)} {c(w,d) + k(1-b+b \frac{|d|}{avdl})} log \frac{M+1}{df(w)}, b \in [0,1], k \in [0, +\infty)
$$

## Further improvement of VSM

- improved instantiation of dimension
  - stemmed words, stop word removal, phrase, latent semantic indexing (word clusters), character n-grams, ...
  - bag-of-words with phrases is often sufficient in practice
  - language-specific and domain-specific tokenization is important to ensure "normalization of terms"
- improved instantiation of similarity function
  - cosine of angle between two vectors?
  - Euclidean?
  - dot product seems still the best

## Further improvement of BM25

- BM25F [Robertson & Zaragoza 09]
  - Use BM25 for documents with structure ("F" = Fields)
  - Key idea: combine the frequency counts of terms in all fields and then apply BM25 (instead of the other way)
- BM25+ [Lv & Zhai 11]
  - Address the problem of over penalization  of long documents by BM25 by adding a small constant  to TF

## Summary of Vector Space Model

- relevance (q, d) = similarity(q, d)
- query and documents are represented as vectors
- heuristic design of ranking function
- major term weighting heuristics
  - TF weighting and transformation
  - IDF weighting
  - Document length normalization
- BM25 and pivoted normalization seem to be most effective

# Implementation of TR Systems

## Typical TR system architecture

- tokenizer
- indexer (offline)
- scorer (online)
- feedback (offline & online)

## Tokenization

- normalize lexical units
  - words with similar meanings should be mapped to the same indexing term
- stemming
  - mapping all inflectional forms of words to the same root form
- some language (e.g., Chinese) post challenges in word segmentation

## Indexing

- indexing = convert documents to data sturctures that enable fast search
- inverted index
- other indices (e.g., document index) may be needed for feedback

## Inverted index

- dictionary (or lexicon)
- postings
- inerted index for fast search
  - single-term query
  - multi-term boolean query
  - multi-term keyword query
  - more eficiente than sequentially scanning docs

## empirical distribution  fo words

- there are stable language-independent patterns in how people use natural languages
- a few words (e.g., "the", "a", "we") occur very frequently; but most words occur rarely
- the most frequent words in one corpus may be rare in another

## Zipf 's Law

* $ rank * frequency \approx constant $
* $ F(w) = \frac {C} {r(w)^\alpha},  \alpha \approx 1, C \approx 0.1 $

## data structures for inverted index

- dictionary: modest size
  - needs fast random access
  - preferred to be in memory
  - hash table, B-tree, trie
- postings: huge
  - sequential access is expected
  - can stay on dist
  - may contain docId, term freq, term pos, etc
  - compression is desirable

# System Implementation - Inverted Index Construction

## constructing inverted index

- the main difficulty is to build a huge index with limited memory
- memory-based methods: not usable for large collections
- sort-based methods
  - step 1: collect local (termID, docID, freq) tuples
  - step 2: sort local tuples (to make "runs")
  - step 3: pair-wise merge runs
  - step 4: output inverted file

## Inverted index compression

- leverage skewed distribution of values and use variable-length encoding
- TF compression
  - small numbers tend to occur far more freqquency than large numbers
  - fewer bits for small (high frequency) integers at the cost of more bits for large integers
- doc ID compression
  - "d-gap" (store difference): d1, d2-d1, d3-d2, ...
  - feasible due to sequential access
- methods: binary code, unary code, ...

# System Implementation - Fast Search

## A general algorithm for ranking documents

$ f(q, d) = f_a(h(g(t_1,d,q,), … , g(t_k,d, q)),f_d(d), f_q(q)) $

- $f_d(d)$ and $f_q(q)$ are pre-computed
- maintain a score accumulator for each d to compute h
- for each query term $t_i$
  - fetch the inverted list ${(d_1,f_1), …, (d_n, f_n)}$, $d_i$ is docID, $f_i$ is term frequency 
  - for each entry $(d_j,f_j)$, compute $g(t_i,d_j,q)$, and updatescore accumulator for doc $d_i$ to incrementally compute $h$.
- adjust the score to compute $f_a$, and sort

## Further improving efficiency

- caching (e.g., query result, list of inverted index)
- keep only the most promising assumulators
- scaling up to the web-scale (need parallel processing)