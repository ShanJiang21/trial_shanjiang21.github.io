The word itself, as an atomic, was introduced in Naive Bayes model and n-gram POS Tagging models, while the word relations were not taking into consideration in these scenarios. Thus, we may need to turn to the structured pattern of *thesaurus/ corpus* to determine the meaning of words in the context.

The section is called **word embeddings** .

`Word2vec` is a popular embedding method, it is fast to train and the code are available on the web, almost everywhere on the Github. The core idea is predict rather than count.

The first question we need to deal with is:

1. How can we represent the meaning of words?

   a. Usually we can use **words, lemmas, senses, and definition.**

   <img src="../img/lemma.png" alt="The lemma" style="zoom:28%;" />

   b. we establish the relationship between words and senses?

   - Synomity: But there is **no** example of perfect synonymy.
     - Filbert/hazelnut
     - couch/sofa
     - Water/H2o
   - Antonymy: Be opposite at the two end of a scale.
   - Similarity: words with similar meanings, not Synonnyms, but share similar element:
     - car, bicycle
     - cow and horse
   - Word Relatedness
     - car, gasoline (related, not similar)
     - Subordinate/superordinate

   c. Taxonomic relationships

   - Semantic frames and roles
     - John hit Bill
     - Bill was hit by John

   d. Connotation and sentiment

   - Valence: pleasantness of the stimulus
   - Arousal: The intensity of emotion
   - Dominance: the degree of control exerted by the stimulus

> The most popular thesaurus for computational purposes is *WordNet*, a large online resource with versions in many languages.

- One use of *WordNet* is to represent **word senses**;
- Address the task of computing **word similarity**.

*Problems with Discrete representations*

- Too coarse

- Sparse

- Subjective

- Expensive

- Hard to compute

  expert = $$[0, 1, 0, 0, 0, 0, 0, 0]$$

  skillful = $$[0, 0, 0, 0, 0, 1, 0, 0]$$



  ---

  >  The meaning of a word is in its use in the language. -- Wittgenstenin <PI43>

  **Model** for Meaning focusing on similarity

  - Each word = a vector
  - Similar words are nearby in space
  - The standard way of representation

  **Three** Kinds of word embeddings

  1. Count-based: simple function of counts of nearby words.
  2. Brown Clusters: Hierarchical clustering
  3. Word2Vec: representation is by training a classifier to distinguish nearby and far-away words.

`SVD` used as a trick for dimension cut-down, this can give us best seperation between features.

<img src="../img/svd.png" alt="The lemma" style="zoom:48%;" />

---

Basic Concepts in Linguistics:

1. A **<u>sense (or word sense)</u>** is a discrete representation of one aspect of the meaning of a word. Loosely following lexicographic tradition, we represent each sense by placing a superscript on the lemma as in $$bank_1$$ and $$bank_2$$.

2. From the sense, we define how the multiple senses are related with each other. <u>**Homonyms**,</u> and the relation between the senses is one of <u>**homonymy**</u>, refers to the  seem relatively unrelated relations between words. This also applies to the sense of *bat*,  meaning ‘club for hitting a ball’ and the one meaning ‘nocturnal flying animal’.

   1. *homographs*: because they are written the same; (bat and bat, bank and bank)
   2. *homophones*: if they are spelled differently but pronounced the same(write and right, or piece and peace).

   ----

   ​																	<u>**Polysemy**</u> vs <u>**homonymy**</u>

   ---

3. ***Metonymy*** is the use of one aspect of a concept or entity to refer to other aspects of the entity or to the entity itself.


### Bugs I encountered along the way

1. How to fit the `synsets` to a [list of words](http://www.nltk.org/api/nltk.tokenize.html#module-nltk.tokenize), and not a single word?

    try running `synsets` in a loop like so, better use **list comprehensions** to get a list of syns:

   ```python
   for token in :
       syn = wn.synsets(token)
   ```
