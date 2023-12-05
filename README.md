# Generative-AI
## Use Watsonx to respond to natural language questions using RAG approach

This notebook contains the steps and code to demonstrate support of Retrieval Augumented Generation in watsonx.ai. It introduces commands for data retrieval, knowledge base building & querying, and model testing.

This notebook uses Python 3.10.

### Objective

Use LLM model, Langchain and Milvus to create a Retrieval Augmented Generation (RAG) system. This will allow us to ask questions about our documents (that were not included in the training data), without fine-tunning the Large Language Model (LLM). When using RAG, if you are given a question, you first do a retrieval step to fetch any relevant documents from a special database, a vector database where these documents were indexed.

Retrieval Augmented Generation (RAG) is a versatile pattern that can unlock a number of use cases requiring factual recall of information, such as querying a knowledge base in natural language.

##### Definitions:

* LLM - Large Language Model
* Langchain - a framework designed to simplify the creation of applications using LLMs
* Vector database - a database that organizes data through high-dimmensional vectors
* Milvus - vector database
* RAG - Retrieval Augmented Generation (see below more details about RAGs)

#### What is a Retrieval Augmented Generation (RAG) system?
Large Language Models (LLMs) has proven their ability to understand context and provide accurate answers to various NLP tasks, including summarization, Q&A, when prompted. While being able to provide very good answers to questions about information that they were trained with, they tend to hallucinate when the topic is about information that they do "not know", i.e. was not included in their training data. Retrieval Augmented Generation combines external resources with LLMs. The main two components of a RAG are therefore a retriever and a generator.

The retriever part can be described as a system that is able to encode our data so that can be easily retrieved the relevant parts of it upon queriying it. The encoding is done using text embeddings, i.e. a model trained to create a vector representation of the information. The best option for implementing a retriever is a vector database. As vector database, there are multiple options, both open source or commercial products. Few examples are ChromaDB, Mevius, FAISS, Pinecone, Weaviate. Our option in this Notebook will be a local instance of ChromaDB (persistent).

For the generator part, the obvious option is a LLM. In this Notebook we will use a quantized LLaMA v2 model, from the Kaggle Models collection.

The orchestration of the retriever and generator will be done using Langchain. A specialized function from Langchain allows us to create the receiver-generator in one line of code.

In its simplest form, RAG requires 3 steps:

- Index knowledge base passages (once)
- Retrieve relevant passage(s) from knowledge base (for every user query)
- Generate a response by feeding retrieved passage into a large language model (for every user query)


#### Data:

##### Knowledge base:
690,000 words worth of cleaned text from Wikipedia.

##### Train and Test datasets: 
There are three question files, one for each year of students: S08, S09, and S10.

The "questionanswerpairs.txt" files contain both the questions and answers. The columns in this file are as follows:

- ArticleTitle is the name of the Wikipedia article from which questions and answers initially came.
- Question is the question.
- Answer is the answer.
- DifficultyFromQuestioner is the prescribed difficulty rating for the question as given to the question-writer.
- DifficultyFromAnswerer is a difficulty rating assigned by the individual who evaluated and answered the question, which may differ from the difficulty in field 4.
- ArticleFile is the name of the file with the relevant article.

Questions that were judged to be poor were discarded from this data set.

There are frequently multiple lines with the same question, which appear if those questions were answered by multiple individuals. https://www.kaggle.com/rtatman/questionanswer-dataset
