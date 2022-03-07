# Data Structures
-   **Vocab** stores data shared across multiple documents
-   To save memory, spaCy encodes all strings to **hash values**
-   Strings are only stored once in the **SringStore** via `nlp.vocab.strings`
-   String store: **lookup table** in both directions

```python
nlp.vocab.strings.add("coffee")
coffee_hash = nlp.vocab.strings["coffee"]
coffee_string = nlp.vocab.strings[coffee_hash]
```

Doc object also has its own Vocab: `doc.vocab.strings`

Vocab cosists of **Lexem** objects. Lexemes are context-independent so they don't have part-of-speech tags, dependencies or entity labels. But they have:
- Attributes like `text`, `orth` (hash)
- Lexical attributes
- **Not** context-dependent part-of-speech tags, dependencies or entity labels

Doc refers to the Vocab:

![Illustration of the words 'I', 'love' and 'coffee' across the Doc, Vocab and StringStore](https://course.spacy.io/vocab_stringstore.png)

*Is it true that getting depending on context attributes takes more time than getting text?*

### Doc object
You can create Doc not only when you process text, but also manually:

```python
from sapcy.tokens import Doc

words = ['Hello', 'word', '!'] 
spaces = [True, False, False] # is the word is followed by a space

doc = Doc(nlp.vocab, words = words, spaces = spaces)
```

### Span object

![Illustration of a Span object within a Doc with token indices](https://course.spacy.io/span_indices.png)

Span takes at least 3 arguments
- the doc it refers to
- start index of the span
- end index of the span

We can create Span manually:

```python
from spacy.tokens import Doc, Span

span = Span(doc, 0, 2)
span_with_label = Span(doc, 0, 2, label='GREETING')
```

`doc.ents` are writeable, you can and antities manually by overwiting it with a list of spans:

```python
doc,ents = [span_with_label]
```

### Best practices

- Convert doc to string as late as possible (don't miss relations and optimize time)
- Use built-in token attributes
- Pass in the shared Vocab

# Word vectors and semantic similarity

SpaCy can compare two objects and predict similarity.
The Doc, Token and Span objects have a `.similarity` method that takes another object and returns a floating point number between 0 and 1, indicating how similar they are.

**Important:** needs a pipeline that has word vectors included, always  go with a pipeline that ends in `md` or `lg` (medium or large).

```python
# Load a larger pipeline with vectors
nlp = spacy.load("en_core_web_md")

# Compare two documents
doc1 = nlp("I like fast food")
doc2 = nlp("I like pizza")
print(doc1.similarity(doc2))

# Compare two tokens
doc = nlp("I like pizza and pasta")
token1 = doc[2]
token2 = doc[4]
print(token1.similarity(token2))

# Compare a document with a token
doc = nlp("I like pizza")
token = nlp("soap")[0]
print(doc.similarity(token))

# Compare a _span_ with a document
span = nlp("I like pizza and pasta")[2:5]
doc = nlp("McDonalds sells burgers")
print(span.similarity(doc))
```

Similarity is determined using **word vectors**, multi-dimensional representations of meanings of words (**cosine similarity** by default). Vectors for objects consisting of several tokens, like the Doc and Span, default to the average of their token vectors.

You can access the vector via the `vector` attribute of the token.

Predicting similarity can be useful for many types of applications. For example:
- recommend a user similar texts based on the ones they have read
-  flag duplicate content, like posts on an online platform (feature for [[Bioquest]])

There's no objective definition of "similarity", it depends on the context and what application needs to do.

*What is the length of the Word2Vec sliding window, how do we change it?*

# Combining predictions and rules

**Statistical models** are useful if your application needs to be able to generalize based on a few examples.

**Rule-based approaches** on the other hand come in handy if there's a more or less finite number of instances you want to find.

![[Pasted image 20220122181232.png]]

After matching and creating the rasulting Span you can get the tokens depending on context:

```python
matcher = Matcher(nlp.vocab)
matcher.add("DOG", [[{"LOWER": "golden"}, {"LOWER": "retriever"}]])
doc = nlp("I have a Golden Retriever")

for match_id, start, end in matcher(doc):
    span = doc[start:end]
    print("Matched span:", span.text)
    # Get the span's root token and root head token
    print("Root token:", span.root.text)
    print("Root head token:", span.root.head.text)
    # Get the previous token and its POS tag
    print("Previous token:", doc[start - 1].text, doc[start - 1].pos_)
```

```out
Matched span: Golden Retriever
Root token: Retriever
Root head token: have
Previous token: a DET
```

Hovewer, there is more eficcient and fast tool called **Phrase matcher**, it takes Doc objects as patterns:

```python
from spacy.matcher import PhraseMatcher

matcher = PhraseMatcher(nlp.vocab)

pattern = nlp("Golden Retriever")
matcher.add("DOG", [pattern])
doc = nlp("I have a Golden Retriever")

# Iterate over the matches
for match_id, start, end in matcher(doc):
    # Get the matched span
    span = doc[start:end]
    print("Matched span:", span.text)
```




