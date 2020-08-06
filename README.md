# Abacus.AI Model Serving

This repo contains documentation and code describing how to produce artifacts for
hosting your custom models using Abacus.AI model serving infrastructure. Once
the artifacts are prepared as described they can be uploaded in the web application.
After the model is verified it can be deployed to serve predictions.

The uploads consists of three parts:
1. Model artifact file
2. Embedding dataset file
3. Verification data

The artifacts specify the model, for example a tensorflow saved model. The verification
data is a list of JSON records each describing a prediction request and an expected
response. The expected response should be built by running the model locally. This
data is used by the service to verify that it is able to correctly load and evaluate
the model.

Different model types required different artifacts and verification files. There is a
notebook for each model type in this repo to illustrate how to produce the required files.

### Embedding Ranking Model

The embedding ranking model consists of a model that maps its input into an embedding
vector and then the embedding vector is matched against a supplied embedding dataset and
the best (smallest) matches for a given distance fuction (`cosine`, `euclidean`, `manhattan`)
is returned. The embedding dataset is a map from term to embedding vectors of the same size
which must match the model output dimension.

```
term,0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15
the,0.08472235,0.13219114,-0.061549846,...
",",-0.02036745,0.0757717,0.04604803,...
...
```

You make a request with the inputs expected by the model and specify the number of results
required and the distance function to use (defaults to `cosine`).

```
{
  "input": [[2478, 122, 507, 11]],
  "num": 5,
  "distance": "cosine"
}
```

The response consits of the best `num` terms together with their respective distance to the
model output.

```
{
  "result": [
     {"term": "Picture", "score": 0.2541214},
     ...
  ]
}
```

A full example of exporting the artifacts for this model type is in [word2vec.ipynb](https://github.com/realityengines/model-serving/blob/master/word2vec.ipynb).
