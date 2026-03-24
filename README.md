# YouTube Channel RAG (Gemini + Local Embeddings)

A Retrieval-Augmented Generation (RAG) application that allows you to "chat" with an entire YouTube channel. Built with Streamlit, this tool fetches video transcripts, embeds them locally to avoid API rate limits, and uses Google's Gemini API to synthesize answers complete with **exact video timestamp citations**.

## Features
* **Bypass Rate Limits:** Uses Hugging Face's local `all-MiniLM-L6-v2` model to embed thousands of transcript chunks for free, bypassing the strict 15 RPM limits on cloud embedding APIs.
* **Exact Timestamp Citations:** Chunks transcripts intelligently so that the source links take you to the exact second the creator mentioned the information.
* **YouTube Anti-Bot Bypass:** Uses a `cookies.txt` file to authenticate as a real user, preventing YouTube from blocking the Google Colab IP address.
* **Lightning Fast Retrieval:** Uses ChromaDB for highly efficient local vector storage.
* **Gemini 2.5 Flash:** Leverages Google's state-of-the-art multimodal model for synthesizing the retrieved context into a clear, accurate answer.

## Architecture
1. **Fetch:** `yt-dlp` scrapes the video IDs from the provided channel URL.
2. **Transcribe:** `youtube-transcript-api` (v1.2.4) fetches manual or auto-generated transcripts using local browser cookies.
3. **Chunk & Embed:** Text is chunked with overlapping windows, mapped to start times, and converted into 384-dimensional vectors using `sentence-transformers`.
4. **Store:** Vectors and metadata are stored in a persistent local `ChromaDB` collection.
5. **Query:** User questions are embedded locally, relevant chunks are retrieved from ChromaDB, and passed to `gemini-2.5-flash` to generate a grounded answer.

## Running on Google Colab

Because YouTube heavily monitors datacenter IPs, running this on Colab requires a few specific steps to bypass blocks and expose the Streamlit UI.

### 1. Prerequisites
* A **Google Gemini API Key** (Get one at [Google AI Studio](https://aistudio.google.com/))
* An **Ngrok Auth Token** (Sign up at [Ngrok](https://ngrok.com/) to tunnel the Streamlit app out of Colab)
* A **`cookies.txt`** file (Export your YouTube session cookies using a browser extension like "Get cookies.txt LOCALLY")

### 2. Setup Instructions
Upload your `cookies.txt` file directly into the base `/content/` directory of your Google Colab environment.

Install the required dependencies:
```bash
!pip install -q streamlit google-generativeai chromadb "youtube-transcript-api==1.2.4" yt-dlp sentence-transformers pyngrok
