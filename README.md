# Telegram Perfume Recommendation Bot

A Telegram-based recommendation system for perfumes using vector similarity search, LLM-assisted query understanding, and user preference profiling.

---

## Overview

The system processes free-text user queries, converts them into vector embeddings, retrieves semantically similar perfumes from a vector database, and generates natural-language recommendations. It supports description-based search, similarity search to a known perfume, and personalized recommendations based on user history.

---

## Architecture

Telegram interaction is implemented using aiogram with FSM-based dialog control.  
Text understanding, note normalization, and response generation are handled by Claude 3.5 Sonnet via OpenRouter.  
Text embeddings are generated using a sentence-transformers model and stored in Weaviate Cloud.  
User profiles and preferences are persisted in a local SQLite database.

---

## Technology Stack

Python 3.11+ is used as the runtime environment.  
Telegram bot logic is implemented with aiogram 3.3.0.  
Embeddings are generated using `all-MiniLM-L6-v2` with a vector size of 384.  
Vector storage and similarity search are handled by Weaviate Cloud with an HNSW index.  
LLM integration is provided by Claude 3.5 Sonnet via OpenRouter API.  
User data is stored in SQLite.

---

## Data Processing

Raw perfume data is loaded from CSV files and preprocessed by normalizing URLs, merging aromatic accords, removing deprecated fields, and generating a unified textual description per perfume.  
The resulting dataset contains only perfume URLs and their generated descriptions and is used as input for vectorization.

---

## Vector Search

All perfume descriptions and user queries are embedded using the same transformer model.  
Search is performed by querying Weaviate with the query embedding to retrieve nearest neighbors.  
Results are reranked using cosine similarity computed on full descriptions to improve precision.

---

## Search Modes

Description-based search validates and normalizes the user query using the LLM, performs vector search, reranks results, and filters candidates below a similarity threshold of 0.5.  
Similarity-based search extracts a reference perfume name via the LLM, builds a query from its notes, applies gender filtering, reranks candidates, and uses a lower similarity threshold of 0.39.

---

## LLM Usage

Claude 3.5 Sonnet is used for query validation, aromatic note normalization, and final response generation.  
Low temperature is applied for validation and normalization tasks, while a moderate temperature is used for recommendation generation.  
All requests are executed with a fixed timeout.

---

## User Profiles

User data is stored in SQLite and includes Telegram user ID, selected gender, liked perfumes, and aggregated liked notes.  
Liked notes are used to construct personalized queries for preference-based recommendations.

---

## Configuration

Create a `.env` file with the following variables:

```env
TOKEN=telegram_bot_token
WEAVIATE_URL=weaviate_cluster_url
WEAVIATE_API_KEY=weaviate_api_key
OPENROUTER_KEY=openrouter_api_key
