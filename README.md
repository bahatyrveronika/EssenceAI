Telegram Perfume Recommendation Bot
Overview
This project implements a Telegram bot for perfume recommendation based on vector similarity search, large language model assistance, and user preference profiling. The system processes free-text user queries, converts them into vector representations, retrieves semantically similar perfumes from a vector database, and generates structured natural-language responses.
Architecture
The system consists of a Telegram bot built with aiogram and FSM-based dialog management, an embedding layer based on sentence-transformers, a vector search layer implemented using Weaviate Cloud, an LLM integration layer using Claude 3.5 Sonnet via OpenRouter, and a local SQLite database for storing user profiles and preferences.
Technology Stack
The project is implemented in Python 3.11+. Telegram interaction is handled by aiogram 3.3.0. Text embeddings are generated using the sentence-transformers model all-MiniLM-L6-v2 with a vector dimension of 384. Vector storage and search are provided by Weaviate Cloud using an HNSW index. Natural language understanding and response generation are performed by Claude 3.5 Sonnet. User data is stored in SQLite.
Data Processing
Source data is provided in CSV format and contains structured perfume information. During preprocessing, URLs are normalized, aromatic accords are merged into a single textual field, obsolete columns are removed, and a unified textual description is generated for each perfume. The final dataset contains perfume URLs and their corresponding descriptions and is used for vectorization.
Vectorization and Search
All perfume descriptions and user queries are embedded using the same transformer model. Search is performed by querying Weaviate with the query embedding to retrieve nearest neighbors. Retrieved candidates are reranked using cosine similarity computed on full descriptions to improve precision.
Search Modes
Description-based search validates and normalizes the user query via the LLM, embeds the normalized text, performs vector search, reranks the results, and filters out candidates below a similarity threshold of 0.5. Similar-perfume search extracts a perfume name using the LLM, builds a query from its notes, performs vector search with gender filtering, applies reranking, and uses a lower similarity threshold of 0.39.
LLM Integration
Claude 3.5 Sonnet is used for query validation, aromatic note normalization, and response generation. Low temperature is applied for validation and normalization tasks, while a moderate temperature is used for generating recommendations. All LLM requests are subject to a fixed timeout.
User Profiling
User profiles are stored in SQLite and contain Telegram user identifiers, selected gender, liked perfumes, and aggregated liked notes. Liked notes are used to construct personalized queries for preference-based recommendations.
Deployment
Configuration is provided via environment variables for the Telegram bot token, Weaviate credentials, and OpenRouter API key. Data preprocessing and vector ingestion are performed once before deployment. The embedding model and database connections are initialized at application startup.
