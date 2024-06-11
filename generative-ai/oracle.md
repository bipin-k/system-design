### Introduction
A language model is a probabilistic model of text. The LM gives a probability to every word in its vocabulary of appearing in the blank.

### Vector Search/ Semantic Search


### LLM Architecture


#### Encoders
Models that convert a sequence of words to an embeddings, vector represenattion,
Encoder models are designed to encode text, that is produce embeddings.
Examples : BERT, SBERT, MiniLM, Embed-light, RoBERTA, DistillBERT, etc,

#### Decoders
Model takes a sequence of words and output next word. Decoder Models are designed to decode or generate text,
Examples: GPT-4, Llama, Bloom, Falcon, etc,

#### Encoders-Decoders
Encodes a sequence of words and use the ecodsing + to output a next word,
Example: T5, UL2, BART, etc,

#### Embedding
The process of converting a sequence of words into a single vector or a sequence of vectors.

#### Text Generation - 

All models are built on transformer archirtecture.

https://proceedings.neurips.cc/paper_files/paper/2017/file/3f5ee243547dee91fbd053c1c4a845aa-Paper.pdf

### Prompting & Promt Engineering

Prompt - The process of providing the text to the LLM as input, sometimes containing instructions and or examples.

Prompt Engineering - The process of iteratively refining a prompt for the purpose of eliciting a particular style of response.

In-context Learning

Few-shot Prompting

k-shot Prompting

Chain of Thought - Prompt the LLM to emit the intermediate reasoning steps,

Least to Most - Prompt the LLM to decompose the problem and solve easy first,

Step Back - Prompt the LLM to identify high level concepts pertinent to a specific task,

Prompt Injection (Jailbreaking) - 


### Training

LLM domain adaptation
Domain adaptation is the process of customizing a generative AI foundation model (FM) that has been trained on massive amounts of public data to increase its knowledge and capabilities for a specific domain or use case.

Fine-Tuning
Parameter-Efficient Fine-Tuning using 🤗 PEFT
Soft Prompting
Continual Pre-Training

LoRA: Low-Rank Adaptation of Large Language Models
Prompt Tuning - Hard Prompts & Soft Prompts

https://proceedings.mlr.press/v202/geiping23a/geiping23a.pdf

### Decoding
Greedy Decoding
Random Sampling
Beam Search

### Hallucination


### LLM Applications

* Code Models
Code-Pilot, Codex, Code Llama

* Multi Modal

Trained on multple modalities like languages and images

* Diffusion Models

* Language Agents

ReAct - Iterative framework where LLM emits thoughts, then acts, and observe result,
Toolformer - Pre-TYraining techniques where strings are replaced with calls to tools that yield result,
Bootstrapped Reasoning


### RAG Framework

Retriever
Ranker
Generator

RAG Sequence Model
RAG Token Model

RAG Pipeline
Ingestion -> Retrieval -> Generation

RAG Evaluation
RAG Triad
Query -> Context -> Responsse
Context Relevance -> Groundness -> Answer Relevance

Keyword Search
BM25 is the default similarity ranking (relevancy) algorithm in Elasticsearch. 

Semantic Search
* Dense Retrieval
* Reranking



LangChain Expression Language LCEL



NOMIC Embedding

### Chunking
Chunking refers to dividing a large text corpus into smaller, manageable pieces or segments. Each chunk acts as a standalone unit of information that can be individually indexed and retrieved. For instance, in the development of RAG models, as Lewis et al. (2020) described, Wikipedia articles are split into disjoint 100-word chunks to create a total of around 21 million documents that serve as the retrieval database. This technique is crucial for enhancing the efficiency and accuracy of the retrieval process, which in turn impacts the overall performance of RAG models in various aspects,

HNSW Index
