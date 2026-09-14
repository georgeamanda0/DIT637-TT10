# DIT637-TT10

## In Root folder 
1. `pip install -r requirements.txt`
   
## Run Ollama Server
1. Open New Terminal
2. `curl -fsSL https://ollama.com/install.sh | sh`
3. `ollama serve`

## Pull and Chat with a Large Language Model (LLM) Conversational AI
1. Open New Terminal
2. `ollama pull gemma2:2b`

## Run Backend ExpressJS DB server (Application Server)
1. Copy your MongoDB Connection String from MongoDB Atlas
2. Create `.env` file and paste it after `MONGODB_URI=` (use `example.env` as reference)
3. Open New Terminal
4. `cd backend-expressjs`
5. `npm install`
7. `npm run start`
8. Make the Port Visibility `Public`
9. Copy the forwarded address ExpressJS

## Run Machine Learning Operations (MLOps) Tracking Server
1. Open New Terminal
2. `cd mlops-mlflow`
3. `pip install -r requirements.txt`
4. `python3 -m mlflow server`

## Run Machine Learning Models
1. `cd mlops-mlflow`
2. `python run_all_models.py`

Note: It will run the following Python scripts
- model_lr is Linear Regression
- model_nn is Neural Network
- model_gb is Gradient Boost
- model_rf is Random Forest

## Run Python Script to create ChromaDB and Insert Data and Run ML Model (Recommender Server)
1. Open New Terminal
2. `cd modelserver-fastapi`
3. `python populate_database.py`
4. `python3 -m uvicorn main:app --reload`

## Run Frontend React Native in the Mobile/Browser
1. Copy your ExpressJS Forwarded Address
2. Create `.env` file and paste it after `API_URL=` following: (use `example.env` as reference)
2. Open New Terminal
4. `cd frontend-reactnative`
5. `npm install`
6. `npx expo start --web`

## EXTRA: Test Ollama Server using curl command
1. New Terminal
2. Copy paste the following:

### Test Generate Response
```
curl http://localhost:11434/api/generate -d '{
  "model": "gemma2:2b",
  "prompt":"Why is the sky blue?"
}'
```
## Test Chat
```
curl -X POST http://localhost:11434/api/chat \
     -H "Content-Type: application/json" \
     -d '{
           "model": "gemma2:2b",
           "messages": [
               { "role": "user", "content": "limit 50 words. please recommed horror movies" }
           ]
         }'
```

### Test ExpressJS Server wrapping Ollama Server
```
curl -X POST http://localhost:3000/chat \
     -H "Content-Type: application/json" \
     -d '{
           "model": "gemma2:2b",
           "messages": [
               { "role": "user", "content": "limit 25 words. please recommed horror movies" }
           ]
         }'
```

### Test FastAPI RAG
```
curl -X POST "http://127.0.0.1:8000/chat" \
-H "Content-Type: application/json" \
-d '{"query_text": "What are the health benefits of green tea?"}'
```

### Test ExpressJS Wrapper FastAPI RAG
```
curl -X POST "http://127.0.0.1:3000/chat" \
-H "Content-Type: application/json" \
-d '{"query_text": "What are the health benefits of green tea?"}'
```
##TT10 - RAG with PDF Data Source 
This lab adds a PDF-based knowledge retrieval system to the movie app
using LangChain and PDF data loaders. The `populate_database.py` script
loads movie PDFs from the data folder, splits them into 800-character
chunks, converts each chunk into a vector using an Ollama embedding
model, and stores those vectors in a Chroma database. When a user asks
a question, `query_rag()` searches Chroma for the five closest chunks
and passes them to the Gemma model as context, so the answer comes from
the source documents rather than the model's own training. This is what
keeps the system from hallucinating — asking about a movie that isn't in
the PDFs returns "I don't know" instead of an invented answer.

### Note on models
Two different Ollama models are used. `nomic-embed-text` handles the
embeddings, since `gemma2:2b` does not support the embeddings endpoint.
`gemma2:2b` is still used to generate the chat responses.