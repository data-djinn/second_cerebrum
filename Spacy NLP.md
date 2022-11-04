[[Python]]  

- the core of spacy is the object containing the processing **pipeline**, usually just named `nlp`
	- `import spacy; nlp = spacy.blank('en')`
	- includes language-specific rules for tokenization, etc
	- when you process a text with the `nlp` object, spaCy creates a `Doc` object - short for "document"
	- the doclets you access information about the text in a structured way, and no information is lost
	- 