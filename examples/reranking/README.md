# Ollama Reranking Implementation

This directory contains examples and documentation for using reranking models with Ollama.

## Qwen3-Reranker Model

The Qwen3-Reranker is a binary classification model that determines relevance between a query and documents.

### Key Features

- **Binary Classification**: Returns yes/no relevance instead of numeric scores
- **Instruction-Based**: Supports custom instructions for different reranking tasks
- **High Performance**: Optimized for fast document ranking

### Usage

1. **Download the model**:
   ```bash
   curl -L -o Qwen3-Reranker-0.6B.f16.gguf https://huggingface.co/mradermacher/Qwen3-Reranker-0.6B-GGUF/resolve/main/Qwen3-Reranker-0.6B.f16.gguf
   ```

2. **Create the model**:
   ```bash
   ollama create qwen3-reranker -f Qwen3-Reranker.Modelfile
   ```

3. **Use the reranking API**:
   ```bash
   curl -X POST http://localhost:11434/api/rerank \
     -H "Content-Type: application/json" \
     -d '{
       "model": "qwen3-reranker",
       "query": "What is the capital of France?",
       "documents": [
         "Paris is the capital of France.",
         "London is the capital of England.", 
         "Berlin is the capital of Germany."
       ],
       "instruction": "Given a web search query, retrieve relevant passages that answer the query"
     }'
   ```

### Response Format

```json
{
  "model": "qwen3-reranker",
  "results": [
    {
      "index": 0,
      "document": "Paris is the capital of France.",
      "relevance_score": 0.9995
    },
    {
      "index": 2,
      "document": "Berlin is the capital of Germany.",
      "relevance_score": 0.0001
    },
    {
      "index": 1,
      "document": "London is the capital of England.",
      "relevance_score": 0.0001
    }
  ]
}
```

### Template Format

The Qwen3-Reranker uses a specific template format that matches the official implementation:

```
<|im_start|>system
Judge whether the Document meets the requirements based on the Query and the Instruct provided. Note that the answer can only be "yes" or "no".<|im_end|>
<|im_start|>user
<Instruct>: {{ .Instruction }}
<Query>: {{ .Query }}
<Document>: {{ .Document }}<|im_end|>
<|im_start|>assistant
<think>

</think>

```

### Implementation Details

- **Scoring Method**: Uses logit probabilities of "yes" vs "no" tokens
- **Input Processing**: Formats queries and documents using the official template
- **Performance**: Leverages Ollama's embedding pipeline for efficient inference
