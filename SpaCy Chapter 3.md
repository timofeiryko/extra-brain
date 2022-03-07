NLP stucture:

![Illustration of the spaCy pipeline transforming a text into a processed Doc](https://course.spacy.io/pipeline.png)

Built-in pipeline components:

![[Pasted image 20220128124738.png]]

Pipeline defined in model's `config.cfg`  (language, components)

Pipeline (`nlp`) attributes:
- nlp.pipe_names: list of pipeline component names
- nlp.pipeline: list of component name + component function tuples (the component functions are the functions applied to the doc to process it)

