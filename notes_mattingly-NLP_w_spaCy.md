- **Video:** https://youtu.be/dIUTsFT2MeQ
- Dr William Mattingly
- Channel: "Python Tutorials for Digital Humanities"
- This course:
- Basic understanding
- How to get the most out of spaCy
- Apply it to your domain
- Project: info extraction
  
  * What is natural language processing?
  
  Different areas:
- Named Entity Recognition (NER)
- Part-of-Speech Tagging (POS)
- Syntactic Parsing
- Text Categorization
- Conference Resolution
- Machine Translation
- NLP vs NLU (natural language understanding)
- NLU: "we train computer to do things like"
	- Relation Extraction
	- Semantic Parsing
	- Paraphrasing
	- Sentiment Analysis
	- Question and Answering
	- Summarization
	- NLP applications: info extraction, text categorization
- "Lawyers use it to extract and classify documents"
- "Finance: extract company names, stocks, ..."
	- "Finds elements you want to extract for you"
- "Text categorization: SPAM detection"
	- Classification
	- [[spaCy]]: "one of many toolkits"
- "I find spaCy to be the best out of them"
	- Good metrics, models out of the shelf
	- Transformer (BERT) models
	- Custom training relatively easy
	- Scales well
		- "Large quantities of documents efficiently"
		- "There's a textbook available": http://spacy.pythonhumanities.com/
		- "How would I go thru the course?"
- First pass thru the video: get an idea of what spaCy can do and what we're going to cover
- Second pass: follow along with the code
- Third pass: "try to do what I describe without checking the video nor the textbook"
- ### Installing spaCy
- Complete instructions at https://spacy.io/usage.
  For Linux, a virtual environment in conda, CPU, and the ability to use and train models in English:
  
  ``` shell
  $ conda create -n spacy
  $ conda activate spacy
  $ conda install -c conda-forge spacy
  $ conda install -c conda-forge spacy-transformers
  # packages only available via pip
  $ pip install spacy-lookups-data
  $ python -m spacy download en_core_web_sm  # the small model
  # or!
  $ python -m spacy download en_core_web_trf  # the more accurate model
  ```
- Installed both models, just in case.
  
  ``` python
  import spacy
  
  # creating an NLP object
  nlp = spacy.load("en_core_web_sm")
  ```
- ### Containers
- Containers contain a large quantity of text.
  Focus on *Doc*, *Span*, *Token*.
- Doc: the thing around everything
- Tokens: words or punctuation marks; index of Doc
- Spans: exists in/out of the Doc obj
	- A token/a sequence of tokens can be an item
- ### spaCy Linguistic Annotations
	- Downloaded the repo for the data: https://github.com/wjbmattingly/freecodecamp_spacy
	- Working with a Doc obj in spaCy.
	  ``` python
	  import spacy
	  
	  nlp = spacy.load("en_core_web_sm")
	  
	  with open("data/wiki_us.txt", "r") as f:
	  text = f.read()
	  
	  print(text)  # standard Wikipedia article
	  ```
	- Creating a Doc object.
	  ``` python
	  doc = nlp(text)
	  - print(doc)
	  ```
	- Looks very similar to the `text` obj. However, the length is different:
	  ``` python
	  print(len(text))  # 3525
	  print(len(doc))  # 652
	  ```
	- Using a `for` loop to understand what's happening.
	  ``` python
	  for token in text[:10]:
	  print(token)  # return letters
	  
	  for token in doc[:10]:
	  print(token)  # return actual tokens
	  ```
	- "Why can't we use the `split` methods?"
- spaCy knows the elements; it will treat them as a "thing," not as characters. Compare the results:
  
  ``` python
  for token in text.split()[:10]:
    print(token)
  ```
- ### Sentence Boundary Detection (SBD)
- Identifying sentences in a text.
  "Why not `split`?"
- Question marks, exclamation marks, periods, ...
  
  #+begin_src python
  for sentence in doc.sents:
    print(sentence)  # prints off every individual sentence
  #+end_src
  
  The larger the models, the better the sentence detector.
  
  #+begin_src python
  sentence1 = doc.sents[0]
  print(sentence1)  # type error: doc.sents is a generator
  
  sentence1 = list(doc.sents)[0]
  #+end_src
  
  *** Token attributes
  
  Token obj provides text, head, ...
  
  #+begin_src python
  token2 = sentence1[2]
  print(token2)  # States
  #+end_src
  
  Attributes of the word:
  
  #+begin_src python
  print(token2.text)  # 'States'
  
  print(token2.left_edge)  # beginning: 'The'
  print(token2.right_edge)  # end: 'America'
  
  print(token2.ent_type)  # 384
  print(token2.ent_type_)  # 'GPE': "geo-political entity"
  #+end_src
  
  For an intro on Named Entity Recognition (NER): http://ner.pythonhumanities.com/intro.html
  
  #+begin_src python
  print(token2.ent_iob_)  # 'I'
  #+end_src
  
  A specific kind of named entity code.
- *'B':* beginning of an entity
- *'I:'* inside of an entity
- *'O:'* outside of an entity
  
  ~'States'~ is /inside/ the entity ~'The United States of America'~
  
  #+begin_src python
  print(token2.lemma_)  # 'States'
  #+end_src
  
  Lemma: "what the word looks like with no inflection"
  What if it's a verb?
  
  #+begin_src python
  sentence1[12].lemma_  # 'know'
  print(sentence1[12].lemma_)  # 'known': original form
  #+end_src
  
  Checking the morphological analysis:
  
  #+begin_src python
  token2.morph
  # NounType=Prop|Number=Sing: proper noun, singular
  sentence1[12].morph
  # Aspect=Perf|Tense=Past|VerbForm=Part: perfect past participle
  #+end_src
  
  "Being good at NLP is also being good with language"
- Spend time and get familiar with these things
- It will be useful when creating rules later
  
  Part-of-speech:
  
  #+begin_src python
  token2.pos_  # 'PROPN': proper noun
  #+end_src
  
  ~pos_~: A simpler grammatical extraction, instead of the more complex morphological extraction.
  
  #+begin_src python
  token2.dep_  # 'nsubj': noun subject
  #+end_src
  
  ~dep_~: dependency relation; what role it plays in the sentence
  
  #+begin_src python
  token2.lang_  # 'en'
  #+end_src
  
  ~lang_~: the language of the doc obj
  
  About 15 more of these token attributes
  
  *** Part-of-speech tagging
  
  #+begin_src python
  text = "Mike enjoys playing football."
  doc2 = nlp(text)
  print(doc2)
  
  for token in doc2:
    print(token.text, token.pos_, token.dep_)
  #+end_src
  
  Will return:
  
  #+begin_src python
  Mike enjoys playing football.
  Mike PROPN nsubj
  enjoys VERB ROOT
  playing VERB xcomp
  football NOUN dobj
  . PUNCT punct
  #+end_src
  
  To represent these visually, use ~displacy~:
  
  #+begin_src python
  from spacy import displacy
  
  displacy.render(doc2, style="dep")  # only works in a notebook?
  #+end_src
  
  *** NER, and how to visualize it
  
  #+begin_src python
  for ent in doc.ents:
    print(ent.text, ent.label_)
  #+end_src
  
  Copied the answer from the book:
  
  #+begin_src python
  The United States of America GPE
  U.S.A. GPE
  USA GPE
  the United States GPE
  U.S. GPE
  US GPE
  America GPE
  North America LOC
  50 CARDINAL
  five CARDINAL
  326 CARDINAL
  Indian NORP
  3.8 million square miles QUANTITY
  9.8 million square kilometers QUANTITY
  fourth ORDINAL
  The United States GPE
  Canada GPE
  Mexico GPE
  Bahamas GPE
  Cuba GPE
  more than 331 million CARDINAL
  third ORDINAL
  Washington GPE
  D.C. GPE
  New York GPE
  Paleo-Indians NORP
  Siberia LOC
  North American NORP
  at least 12,000 years ago DATE
  European NORP
  the 16th century DATE
  The United States GPE
  thirteen CARDINAL
  British NORP
  the East Coast LOC
  Great Britain GPE
  the American Revolutionary War ORG
  the late 18th century DATE
  U.S. GPE
  North America LOC
  Native Americans NORP
  1848 DATE
  the United States GPE
  United States GPE
  the second half of the 19th century DATE
  the American Civil War EVENT
  The Spanishâ€“American War and World War EVENT
  U.S. GPE
  World War II EVENT
  the Cold War EVENT
  the United States GPE
  the Korean War EVENT
  the Vietnam War EVENT
  the Soviet Union GPE
  two CARDINAL
  the Space Race FAC
  1969 DATE
  first ORDINAL
  The Soviet Union's GPE
  1991 DATE
  the Cold War EVENT
  the United States GPE
  The United States GPE
  three CARDINAL
  the United Nations ORG
  World Bank ORG
  International Monetary Fund ORG
  Organization of American States ORG
  NATO ORG
  the United Nations Security Council ORG
  centuries DATE
  The United States GPE
  approximately a quarter DATE
  the United States GPE
  second ORDINAL
  only 4.2% PERCENT
  29.4% PERCENT
  more than a third CARDINAL
  #+end_src
  
  This model makes mistakes: ~the American Revolutionary War~ is not an ~ORG~ :)
  If he was using a larger model, this would be correctly identified as an ~EVENT~
- "This small model doesn't contain word vectors, though"
  
  #+begin_src python
  displacy.render(doc, style="ent")  # only on notebook
  #+end_src
  
  "A lot of Wikipedia data are included in these models"
  
  "How the doc object contains the attributes like ~sents~, ~ents~"
  
  * Word vectors & spaCy
  
  #+begin_src python
  import spacy
  !python -m spacy download en_core_web_md  # downloads medium model
  #+end_src
  
  This medium model has word vectors.
- ## Word vectors
- "Computers to understand what a word actually means"
  Old approaches: /bag of words/
- Won't work for text understanding, though
  
  Word vector: multi-dimensional representation
- Stored as an array
  
  #+begin_src python
  sentence1[0].vector
  #+end_src
  
  ~word2vec~: figures out meaning and relationship from the input text
  
  #+begin_src python
  nlp = spacy.load("en_core_web_md")
  
  with open("data/wiki_us.txt", "r") as f:
    text = f.read()
  
  doc = nlp(text)
  sentence1 = list(doc.sents)[0]
  print(sentence1)
  #+end_src
  
  How to use word vectors with spaCy: getting words that are most similar to "country"
  
  #+begin_src python
  import numpy as nlp
  
  your_word = "country"
  
  ms = nlp.vocab.vectors.most_similar(
    np.asarray([nlp.vocab.vectors[nlp.vocab.strings[your_word]]]), n=10)
  words = [nlp.vocab.strings[w] for w in ms[0][0]]
  distances = ms[2]
  
  print(words)
  #+end_src
  
  Not "synonym" similarity, though
  
  Calculating document similarity:
- ``` python
  nlp = spacy.load("en_core_web_md")  # make sure to use larger package!
  doc1 = nlp("I like salty fries and hamburgers.")
  doc2 = nlp("Fast food tastes very good.")
  
  # Similarity of two documents
  print(doc1, "<->", doc2, doc1.similarity(doc2))  # 0.77 something
  ```
- (stopped at 1h00')