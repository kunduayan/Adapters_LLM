A Sentence-Transformer bi-encoder model, like "all-mpnet-base-v2", leverages a bi-encoder architecture specifically for generating dense vector embeddings of sentences. These embeddings are designed to be semantically meaningful, meaning that sentences with similar meanings will have embeddings that are close to each other in vector space.

Here's the layer-wise architecture and process for a typical Sentence-Transformer bi-encoder:

Input Text:

Two independent sentences or text passages are provided as input (e.g., a query and a document).
Tokenization:

Each input text is first tokenized using a WordPiece tokenizer. This breaks down the text into subword units, along with special tokens like [CLS], [SEP], [PAD], [UNK], and [MASK].
Example: "This is a sentence." might become [CLS] "this" "is" "a" "sen" "##tence" "." [SEP].
Independent Encoders (Shared Weights):

The core of the bi-encoder is two separate encoder branches, but critically, these branches typically share the same underlying Transformer model weights. This means they are effectively identical copies of the same pre-trained model.
Each tokenized input sequence ([CLS] token1 token2 ... [SEP]) is fed into its respective encoder.
Encoder Architecture (e.g., MPNet-base): Each encoder is a stack of Transformer layers. A standard Transformer encoder layer generally includes:
Multi-Head Self-Attention: Computes attention weights between all tokens in the input sequence, allowing the model to capture dependencies and context.
Residual Connections & Layer Normalization: Applied after the attention and feed-forward sub-layers to aid training stability and gradient flow.
Feed-Forward Network (FFN): A position-wise fully connected network that further processes the information.
Pooling Layer:

After the final Transformer layer, each encoder outputs a sequence of contextualized token embeddings. To get a single fixed-size sentence embedding, a pooling operation is applied.
The most common pooling method in Sentence-Transformers is Mean Pooling (average pooling). This calculates the element-wise mean of all token embeddings in the sequence (excluding padding tokens).
Alternatively, the embedding of the [CLS] token (which is designed to aggregate sequence information) can be used.
Normalization (Optional but Common):

The resulting sentence embeddings are often L2-normalized (made into unit vectors). This is a common practice before calculating similarity, especially with cosine similarity, as it ensures the magnitude of the vector doesn't influence the similarity score.
Similarity Calculation:

Finally, the two independent sentence embeddings (one for the query, one for the document) are compared using a similarity function, most commonly cosine similarity.
This function calculates the cosine of the angle between the two vectors, yielding a score between -1 and 1, where 1 indicates perfect similarity and -1 indicates perfect dissimilarity.
Training of a Sentence-Transformer Bi-Encoder:

Sentence-Transformers are typically fine-tuned using contrastive learning objectives on large datasets of sentence pairs (or triplets). The goal of this training is to learn embeddings such that semantically similar sentences are close together in the vector space, and dissimilar sentences are far apart.
During training, the weights of the shared encoder model are updated based on these objectives.
This architecture allows for efficient retrieval by pre-computing and storing embeddings for a large corpus of documents. When a query comes in, its embedding is computed once, and then a fast similarity search can be performed against the pre-indexed document embeddings.
