# learn-weaviate

Continuing on with my [`learn-*`](https://github.com/tlofreso?tab=repositories&q=learn-&type=&language=&sort=) repos, [Weaviate](https://weaviate.io/) is next. Weaviate is an up and coming Vector DB, with some unique features like built-in hybrid search, and re-ranking. I'll begin with their [Quickstart](https://weaviate.io/developers/weaviate/quickstart), then try to use it for a real world example: Extracting Mass times from bulletins... A project I've been working off and on with some friends [here](https://github.com/tlofreso/bulletin).

## Notes from the Quickstart
After the initial bootstrapping, the tutorial walks you through two types of queries: Semantic and Generative

### Semantic Search
A similarity search that looks for objects in Weaviate whose vectors are most similar to the vector for the given input text.

Retrieve objects whose vectors are most similar to that of biology:
```python
questions = client.collections.get("Question")

response = questions.query.near_text(
    query="biology",
    limit=2
)

# output
"""
{
    'answer': 'DNA',
    'question': 'In 1953 Watson & Crick built a model of the molecular structure of this, the gene-carrying substance',
    'category': 'SCIENCE'
}
{
    'answer': 'species',
    'question': "2000 news: the Gunnison sage grouse isn't just another northern sage grouse, but a new one of this classification",
    'category': 'SCIENCE'
}
"""
```

Add a filter that ensures the "category" is equal to "ANIMALS"
```python
questions = client.collections.get("Question")

response = questions.query.near_text(
    query="biology",
    limit=2,
    filters=wvc.query.Filter.by_property("category").equal("ANIMALS")
)

# output
"""
{'answer': 'the nose or snout', 'question': 'The gavial looks very much like a crocodile except for this bodily feature', 'category': 'ANIMALS'}
{'answer': 'Elephant', 'question': "It's the only living mammal in the order Proboseidea", 'category': 'ANIMALS'}
"""
```

### Generative Search
A generative search (or RAG) prompts an LLM with a user query, as well as data retrieved from the database.

Example...
```python
questions = client.collections.get("Question")

response = questions.generate.near_text(
    query="biology",
    limit=2,
    single_prompt="Explain {answer} as you might to a five-year-old."
)

print(response.objects[0].generated)

# output
"""
DNA is like a recipe book that tells our bodies how to grow and work. It is made up of tiny instructions that are passed down    
from our parents and help make us who we are. Just like how a recipe tells you how to make a cake, DNA tells our bodies how to make us!
"""
```

Example 2, writing a tweet
```python
questions = client.collections.get("Question")

response = questions.generate.near_text(
    query="biology",
    limit=2,
    grouped_task="Write a tweet with emojis about these facts."
)

print(response.generated)  # Inspect the generated text

# output
"""
🧬 In  1953, Watson & Crick built a model of the molecular structure of DNA, the gene-carrying substance! 🧬🔬  #Science

🐦 2000 news: The Gunnison sage grouse isn't just another northern sage grouse, but a new species! 🦆🌿 #Science #Discovery
"""
```

Next, I'd like to do the tutorial with my own embeddings. covered [here](https://weaviate.io/developers/weaviate/starter-guides/custom-vectors).