# Natural Language Content Analysis

## An example of NLP

- Lexical analysis (part-of-speech tagging)
- Syntactic analysis (Parsing)
- Semantic analysis
- Inference
- Pragmatic analysis (speech act)

## Examples of Challenges

- Word-level ambiguity
- Syntactic ambiguity
- Anaphora resolution
- Prosupposition

## NLP for Text Retrieval

- must be general robust and efficient -> shallow NLP
- "Bag of words" representation tends to be sufficient
- some text retrieval techniques can naturally address NLP problems
- deeper NLP is needed for complex search tasks

# Text Access

## Two Modes of Text Access: Pull vs. Push

- Pull Modes (search engines)
  - users take initiative
  - ad hoc information need
- Push Mode (recommender systems)
  - system take initiative
  - stable information need  or system has good knowledge about a user's need

## Pull Mode: Querying vs. Browsing

- Querying
- Browsing

## Text Retrieval Problom

## formal formulation of TR

- vocabulary


- query
- document
- collection
- set of relevant documents
  - generally unknown and user-depedent
  - query is a "hint" on which doc is in R(q)
- Task = compute R'(q), an approximation of R(q)

## How to compute R'(q)

- Strategy 1: Document selection
  - system must decide if a doc is relevant or not
- Strategy 2: Documen ranking
  - system only needs to decide if a doc is more likely relevant than another (relative relevance)

### prblems of document selection

- the classifier is unlikely accurate
  - "over-constrained" query -> no relevant docs to return
  - "under-constrained" query -> over delivery
  - hard to find the right position between these two extremes
- even if it is accurate, all relevant docs are not equally relevant
  - prioritization is needed
- thus, ranking is generally preferred

# Overview of Text Retrieval Methods

## How to design a ranking function

- a good ranking function should rank relevant documents on top of non-relevant ones
- Key challenge: how to measure the likelihood that document d is relevant to query q
- retrieval model = formalization of relevance

## Many different retrieval models

- similarity-based models: f(q,d) = similarity(q,d)
  - vecotor space model
- probabilistic model: f(q, d) = p(R=1|d, q)
  - classic probabilistic model
  - language model
  - divergence-from randomness model
- probabilistic inference model: f(q,d) = p(d->q)
- axiomatic model: f(q,d) must satisfy a set of constraints

## Common ideas in state of art retrieval models

- bag of words
- term frequency of words (TF): c(word, d)
- document length: |d|
- document frequency words (df): df(w) = p(w|collection-of-docs)

## which model works the best

- pivoted length normlization
- BM25 (most popular)
- query likelyhood
- PL2

# Vector space model - basic idea

## VSM is a framework

- represent a doc/query by a term vector
  - term: basic concepts
  - each term defines one dimension
  - N terms define an N-dimensional space
  - query vector
  - doc vector

### what VSM doesn't say

- how to define/select the "basic concept"
- how to place docs and query in the space (= how to assign term weights)
  - term weight in query indicates importance of term
  - term weight in doc indicates how well the term caracterizeds the doc
- how to define the similarity measure

# Vector Space Retrieval Model - Simplest Instantiation

- Dimension instantiation: bag of words (BOW)
- vector placement: 0-1 bit vector (word presence / absence)
- similarity instantiation: dot product
- f(q,d) = number of distinct query words matched in doc