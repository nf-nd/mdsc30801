# mdsc30801
Project Repository for my course final project

Title: U.S. Monetary Policy RAG Assistant
Domain: U.S. Monetary Policy since 2020. The topic was selected because of my interest in Monetary Policy, my background studying Central Banking. and relevance to Current Events.
  Problem: The Fed is very particular about its language regarding the future path of interest rates, notably because it deliberately guides market expectations as a policy tool. Because of this, it is important to be able to parse the language and context of statements, in order to get an accurate picture of what the Fed is saying interest rates will do.

Architecture: Retrieval-augmented generation (RAG) is an end-to-end architecture that combines LLM generation with external information retrieval to improve the relevance and support of responses. In this project I create a user-facing application function as the entry point, capturing both the user query and runtime parameters (for example, model selection and the retrieval “top‑K” setting that determines how many candidate passages are returned). The orchestration layer then maintains continuity across interactions by managing session state and coordinating dependencies among components, enabling the system to behave as a unified pipeline rather than a set of isolated instances.

Pipeline: Within the pipeline, an agent-mediated reasoning layer determines when retrieval should be invoked and how retrieved evidence should be incorporated into the LLM prompt, reflecting the three step RAG pattern of retrieval, augmentation, and generation. On the retrieval side, the query is represented in a vector space and matched against a vector store to identify semantically similar document chunks (by looking at Euclidean proximity in vector space), which are subsequently provided as contextual inputs to the generator. The final stage aggregates the retrieved context and generated text into a response that can include supporting sources, reinforcing the interpretability and verifiability of the resulting output and underscoring the complementary roles of a predictable pipeline structure and an agent capable of tool-mediated decision-making.

Document Collection: The documents included are a collection of FOMC press releases from 2020 2025 and Fed Minutes for the 2024 and 2025 meetings. The FOMC press releases from 2020-2025 were included because they are the main communication documents of the FOMC (the rate setting committee), and as such include the interest rate decision as well as intentional Fed communications regarding current leading indicators and the path of future interest rates. The Fed Minutes are included because they go into more depth regarding the thoughts of the FOMC. I felt a RAG agent would be able to better analyze the recent and future path of interest rate decisions if this in-depth document base was more recent and limited, which is why it only goes back to 2024.

Agent Configuration: The Agent assumes the role of a monetary policy correspondent. I assign it the goal of acting as an expert in monetary policy with a vocabulary of an economist, in order to make sure its answers are more accurate and precise. I used Chat-GPT-4o-mini for this project. This model is only trained on data up until October 2023. Documents in my RAG extend beyond this date. This initially presented a problem in that the model has an automatic response to respond to any query asking it to reference data beyond October 2023 with a null result. To circumvent this limitation and allow it to reference the RAG, I specify in its backstory that it is pretending the date is December 15, 2025. Lastly, i specify in its expected output for it to always reference the RAG database. Without this specification, it pulls from its training data instead of the RAG, which is more detailed. Here, I also specify some diction nice-to-haves, like using basis points instead of percents.

## Installation and Setup Instructions

## Installation
1. **Download and Install VS Code**

2. **Install Python dependencies**
   - `pip install -r requirements.txt` (installs Streamlit, DuckDB, SentenceTransformers, and CrewAI versions relevantto this project).


## Required project assets

- **DuckDB database file**
  - The app expects a DuckDB file path (the default is `backend/fed_vector.duckdb`) and will stop if it doesn’t exist.
  - The DuckDB file must contain a table named `fed_rag_documents` with at least:
    - `text` (the passage/chunk text)
    - `embedding` (a vector/array compatible with DuckDB’s `array_cosine_similarity`)

- **Embedding model configuration**
  - The embedder is set to `all-MiniLM-L6-v2` with dimension `384` in `config.py`; the DB embeddings must match this dimension because queries are cast to `FLOAT[EMBEDDING_DIMENSION]` during retrieval.

## Running the app

1. **Start Streamlit**
   - From the directory containing `app.py`:  
     - `streamlit run app.py` (the UI, chat, and sidebar live here).

2. **Configure in the sidebar (first run)**
   - Paste your **OpenAI API key** into the “OpenAI API Key” field; the app sets it as `OPENAI_API_KEY` and will halt until it’s provided.
   - Set **Database Path** to your `.duckdb` file (or leave the default `backend/fed_vector.duckdb` if that file exists).
   - Adjust:
     - **Top K Passages** (how many chunks are retrieved per search).
     - **Max Tool Calls** (caps how many retrieval iterations the agent can do per question).
     - **LLM Model** (currently only `gpt-4o-mini` is listed in `AVAILABLE_MODELS`).

## Quick troubleshooting

- “Database not found…”: confirm the DuckDB file exists at the sidebar path; the app explicitly checks file existence and stops if missing.
- Retrieval errors mentioning dimensions: ensure your stored embeddings are the same size as `EMBEDDING_DIMENSION = 384` in `config.py`.
- No answer until API key: the app blocks execution until a key is entered in the sidebar.


Streamlit Deployment: [https://mdsc30801-fletes.streamlit.app]
