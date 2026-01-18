# Embeddings e Vector Databases

## Índice

- [O que são Embeddings?](#o-que-são-embeddings)
- [Conceitos Principais](#conceitos-principais)
  - [1. O que são Embeddings](#1-o-que-são-embeddings)
  - [2. Similarity Search](#2-similarity-search)
  - [3. Vector Databases](#3-vector-databases)
  - [4. Qdrant - Vector Database](#4-qdrant---vector-database)
- [Como Funciona](#como-funciona)
  - [1. Criando Embeddings](#1-criando-embeddings)
  - [2. Armazenando em Vector DB](#2-armazenando-em-vector-db)
  - [3. Buscando por Similaridade](#3-buscando-por-similaridade)
- [Integração Embeddings + LLM](#integração-embeddings--llm)
- [Melhores Práticas](#melhores-práticas)
- [Casos de Uso Comuns](#casos-de-uso-comuns)
- [Próximos Passos](#próximos-passos)

---

## O que são Embeddings?

Embeddings são representações numéricas (vetores) de texto, imagens ou outros dados que capturam o significado semântico. Em vez de comparar palavras exatas, embeddings permitem comparar o significado e contexto.

### Características Principais:

- **Representação numérica**: Texto convertido em arrays de números
- **Semântica preservada**: Palavras similares em significado têm vetores próximos
- **Dimensão fixa**: Cada embedding tem um tamanho consistente (ex: 1536 dimensões)
- **Busca semântica**: Encontrar documentos similares sem palavras-chave exatas

**Exemplo Visual:**
```
"gato" → [0.2, -0.1, 0.5, ..., 0.8]  (1536 números)
"felino" → [0.21, -0.09, 0.52, ..., 0.79]  (vetores próximos!)
"carro" → [-0.5, 0.3, -0.2, ..., 0.1]  (vetores distantes)
```

---

## Conceitos Principais

### 1. **O que são Embeddings**

Embeddings são gerados por modelos de linguagem (como text-embedding-ada-002 da OpenAI) que foram treinados em bilhões de textos para entender relacionamentos semânticos.

**Como funciona:**
1. **Input**: Texto (frase, parágrafo, documento)
2. **Processamento**: Modelo de embedding processa o texto
3. **Output**: Vetor numérico (ex: 1536 números)

**Propriedades:**
- **Similaridade semântica**: Textos com significado similar → vetores próximos
- **Distância euclidiana**: Quanto menor a distância, mais similar
- **Cosine similarity**: Medida comum de similaridade (0 a 1)

**📍 Documentação:** [OpenAI Embeddings](https://platform.openai.com/docs/guides/embeddings)

### 2. **Similarity Search**

Similarity search é a busca de documentos semelhantes usando embeddings em vez de palavras-chave exatas.

**Processo:**
1. Converter query de busca em embedding
2. Calcular distância/cosseno entre query e todos os embeddings armazenados
3. Retornar documentos com maior similaridade

**Exemplo:**
```python
# Query: "Como funciona machine learning?"
# Embedding da query: [0.5, -0.2, 0.8, ...]

# Documentos armazenados:
# Doc1: "Aprendizado de máquina usa algoritmos" → [0.48, -0.19, 0.79, ...]  (similaridade: 0.95)
# Doc2: "Python é uma linguagem" → [0.1, 0.3, -0.5, ...]  (similaridade: 0.23)

# Resultado: Doc1 é retornado (mais similar)
```

### 3. **Vector Databases**

Vector databases são bancos de dados otimizados para armazenar e buscar embeddings eficientemente.

**Características:**
- Armazenamento de vetores de alta dimensão
- Indexação para busca rápida (não busca linear em todos os vetores)
- Suporte para similarity search (cosine, euclidean, dot product)
- Escalabilidade para milhões de vetores

**Comparação com DB tradicional:**
| Aspecto | DB Tradicional | Vector DB |
|---------|----------------|-----------|
| Busca | Palavras-chave exatas | Similaridade semântica |
| Indexação | B-tree, hash | HNSW, IVF |
| Escalabilidade | Limitada para busca semântica | Otimizada para vetores |
| Caso de uso | Dados estruturados | Dados semânticos (texto, imagens) |

**Vector Databases populares:**
- **Qdrant**: Open source, Python-first
- **Pinecone**: Cloud managed, fácil de usar
- **Weaviate**: Open source, GraphQL API
- **Chroma**: Open source, Python

### 4. **Qdrant - Vector Database**

Qdrant é um vector database open source escrito em Rust, otimizado para similarity search.

**Características:**
- **Performance**: Escrita e leitura rápidas
- **Escalabilidade**: Suporta milhões de vetores
- **API REST**: Interface simples via HTTP
- **Python SDK**: Cliente Python nativo
- **Deployment**: Local, Docker, ou Cloud

**Conceitos do Qdrant:**
- **Collection**: Container para vetores (similar a tabela)
- **Points**: Vetores armazenados com ID e payload (metadados)
- **Payload**: Dados adicionais associados ao vetor (ex: texto original, tags)
- **Query**: Busca por similaridade ou filtros

**📍 Documentação:** [Qdrant Documentation](https://qdrant.tech/documentation/)

---

## Como Funciona

### 1. **Criando Embeddings**

**Usando OpenAI API:**
```python
from openai import OpenAI
import os
from dotenv import load_dotenv

load_dotenv()
client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

def criar_embedding(texto):
    response = client.embeddings.create(
        model="text-embedding-ada-002",
        input=texto
    )
    return response.data[0].embedding

# Exemplo
texto = "Machine learning é uma área da IA"
embedding = criar_embedding(texto)
print(f"Dimensão do embedding: {len(embedding)}")  # 1536
```

**Características:**
- Modelo: `text-embedding-ada-002` (OpenAI)
- Dimensão: 1536 números
- Custo: Baixo (ex: $0.0001 por 1K tokens)
- Input: Até 8191 tokens por texto

### 2. **Armazenando em Vector DB**

**Setup Qdrant (Local):**
```python
from qdrant_client import QdrantClient
from qdrant_client.models import Distance, VectorParams, PointStruct

# Conectar ao Qdrant local
client = QdrantClient("localhost", port=6333)

# Criar collection
client.create_collection(
    collection_name="documentos",
    vectors_config=VectorParams(size=1536, distance=Distance.COSINE)
)

# Adicionar vetor com metadados (payload)
client.upsert(
    collection_name="documentos",
    points=[
        PointStruct(
            id=1,
            vector=embedding,
            payload={"texto": "Machine learning é uma área da IA", "categoria": "tecnologia"}
        )
    ]
)
```

**Payload (Metadados):**
O payload permite armazenar informações adicionais que podem ser usadas para filtragem:
- Texto original
- Categoria, tags
- Data de criação
- Autor, fonte
- Qualquer dado estruturado (JSON)

### 3. **Buscando por Similaridade**

**Busca simples:**
```python
# Buscar documentos similares
results = client.search(
    collection_name="documentos",
    query_vector=query_embedding,
    limit=5  # Top 5 resultados
)

for result in results:
    print(f"ID: {result.id}, Score: {result.score}")
    print(f"Texto: {result.payload['texto']}")
```

**Busca com filtros:**
```python
from qdrant_client.models import Filter, FieldCondition, MatchValue

# Buscar apenas documentos da categoria "tecnologia"
results = client.search(
    collection_name="documentos",
    query_vector=query_embedding,
    query_filter=Filter(
        must=[
            FieldCondition(key="categoria", match=MatchValue(value="tecnologia"))
        ]
    ),
    limit=5
)
```

**Tipos de busca:**
- **Similarity search**: Busca por vetor mais similar
- **Filtered search**: Busca com filtros de metadados
- **Hybrid search**: Combinação de similarity + filtros
- **Batch search**: Múltiplas queries de uma vez

---

## Integração Embeddings + LLM

Embeddings são fundamentais para RAG (Retrieval-Augmented Generation):

**Fluxo RAG:**
1. **Indexação**: Criar embeddings de documentos e armazenar
2. **Query**: Usuário faz pergunta
3. **Retrieval**: Buscar documentos similares usando embedding da query
4. **Context**: Usar documentos recuperados como contexto
5. **Generation**: LLM gera resposta baseada no contexto

**Exemplo prático:**
```python
# 1. Criar embedding da query do usuário
query = "Como funciona machine learning?"
query_embedding = criar_embedding(query)

# 2. Buscar documentos similares
results = client.search(
    collection_name="documentos",
    query_vector=query_embedding,
    limit=3
)

# 3. Extrair textos relevantes
contexto = "\n".join([r.payload["texto"] for r in results])

# 4. Usar contexto na chamada LLM
prompt = f"""Baseado no contexto abaixo, responda a pergunta:

Contexto:
{contexto}

Pergunta: {query}
Resposta:"""

resposta = chamar_llm(prompt)
```

---

## Melhores Práticas

1. **Dimensão do embedding**: Use modelo consistente (ex: 1536 para ada-002)
2. **Chunking**: Quebre documentos longos em chunks menores para melhor precisão
3. **Payload**: Armazene texto original e metadados úteis para filtragem
4. **Normalização**: Alguns vector DBs normalizam vetores automaticamente (Qdrant sim)
5. **Batch operations**: Use upsert em batch para melhor performance
6. **Filtros**: Use payload para filtrar antes de calcular similaridade (mais rápido)
7. **Índices**: Qdrant cria índices automaticamente, mas ajuste conforme necessário
8. **Custos**: Monitore custos de criação de embeddings (API OpenAI)

## Casos de Uso Comuns

- **Busca semântica**: Encontrar documentos sem palavras-chave exatas
- **RAG**: Retrieval-Augmented Generation para chatbots
- **Recomendações**: Recomendar produtos/conteúdo similar
- **Deduplicação**: Encontrar documentos duplicados
- **Clustering**: Agrupar documentos por similaridade
- **Question Answering**: Sistema de perguntas e respostas sobre documentos

## Próximos Passos

Após entender embeddings e vector databases, você estará pronto para:

- Implementar sistema completo de busca por similaridade
- Construir arquitetura RAG com embeddings + LLM
- Otimizar chunking e indexação de documentos
- Integrar vector DB com backend Node.js
- Explorar técnicas avançadas de retrieval (re-ranking, hybrid search)

---

**Recursos Oficiais:**

- [OpenAI Embeddings Guide](https://platform.openai.com/docs/guides/embeddings)
- [Qdrant Documentation](https://qdrant.tech/documentation/)
- [Qdrant Python Client](https://github.com/qdrant/qdrant-client)
- [Vector Database Comparison](https://www.pinecone.io/learn/vector-database/)
