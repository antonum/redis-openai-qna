

<img src="./app/assets/RedisOpenAI.png" alt="Drawing" style="width: 100%;"/> </td>


# Question & Answering using LangChain, Redis & OpenAI

**LangChain** simplifies the development of LLM applications through modular components and "chains". It acts as a wrapper around several complex tools and makes us more efficient in our development workflow.

**Redis** plays a crucial role with large language models (LLMs) for a few resons. It can store and retrieve data in near realtime (for caching) and can also index vector embeddings for semantic search. Semantic search enables the LLM to attach to external memory or "knowledge" to help augment the LLM prompts and ensure greater quality in results. Redis has both the developer community and enterprise-readiness required to deploy quality AI-enabled applications in this demanding marketplace.

**OpenAI** is shaping the future of next-gen apps through it's release of powerful natural language and computer vision models that are used in a variety of downstream tasks.

This example Streamlit app gives you the tools to get up and running with **Redis** as a vector database, **OpenAI** as a LLM provider for embedding creation and text generation, and **LangChain** for application dev. *The combination of these is what makes things happen.*

![ref arch](app/assets/RedisOpenAI-QnA-Architecture.drawio.png)

____

## Create dev env (*optional*)
Use the provided conda env for development:
```bash
conda env create -f environment.yml
```

## Run the App
The example QnA application uses a dataset from wikipedia of articles about the 2020 summer olympics. The **first time you run the app** -- all docs will be downloaded, processed, and stored in Redis. This will take a few minutes to spin up initially. From that point forward, the app should be quicker to load.

### Use Docker Compose
Create your env file:
```bash
$ cp .env.template .env
```
*fill out values, most importantly, your `OPENAI_API_KEY`.*

Run with docker compose:
```bash
$ docker compose up
```
*add `-d` option to daemonize the processes to the background if you wish.*

Navigate to:
```
http://localhost:8080/
```

### Using Azure OpenAI

If using Azure OpenAI - use `.env.azure.template` instead. Besides `OPENAI_API_KEY`, make sure to fill in your own value for `OPENAI_API_BASE` and create Model Deployment for the engines such as `text-davinci-003`.

Currently (May 2023) Embedding Generation on Azure OpenAI is limited in batch size and request frequency, that makes it unusable for the purposesof this demo. In a meantime if `OPENAI_API_TYPE=azure` - this demo would load and use HuggingFace `all-MiniLM-L6-v2` for embeddings. You might also request service limit increase for your Azure subscription to address it. 

### Using Llama.cpp with Alpaca model

WARNING! While use of of locally run llama.cpp-based model is possible, it will be extremely slow, comparing to calling out to the hosted OpenAI. 3-5 minutes for producing an answer to a single question on Mac M1.

Llama.cpp can be used without any API key locally. You need to download the model locally from https://huggingface.co/Sosaka/Alpaca-native-4bit-ggml/blob/main/ggml-alpaca-7b-q4.bin and expose it in the local file system. For example:
```
LLAMA_CPP_MODEL_PATH=/models/ggml-alpaca-7b-q4.bin
```
Now mount the file in the docker container:
```
  docker run -it -p 80:80 \
    --env-file=.env.llama_cpp.template \
    -v "${PWD}/models":/models \
    redis-openai-qna
    

**NOW: Ask the app anything about the 2020 Summer Olympics!**