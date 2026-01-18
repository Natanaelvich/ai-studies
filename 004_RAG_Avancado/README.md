# RAG Avançado - Chunking, Metadata e Re-ranking

## Índice

- [O que são Técnicas Avançadas de RAG?](#o-que-são-técnicas-avançadas-de-rag)
- [Chunking Strategies](#chunking-strategies)
  - [1. Chunking por Tamanho](#1-chunking-por-tamanho)
  - [2. Chunking Hierárquico](#2-chunking-hierárquico)
  - [3. Chunking Semântico](#3-chunking-semântico)
- [Metadata Filtering](#metadata-filtering)
  - [1. Filtrar por Tipo](#1-filtrar-por-tipo)
  - [2. Filtrar por Data](#2-filtrar-por-data)
  - [3. Filtrar por Fonte](#3-filtrar-por-fonte)
- [Re-ranking](#re-ranking)
  - [1. Cross-Encoder](#1-cross-encoder)
  - [2. LLM-based Re-ranking](#2-llm-based-re-ranking)
- [Melhores Práticas](#melhores-práticas)
- [Casos de Uso Comuns](#casos-de-uso-comuns)
- [Próximos Passos](#próximos-passos)

---

## O que são Técnicas Avançadas de RAG?

Técnicas avançadas de RAG melhoram a qualidade e precisão do sistema através de:
- **Chunking Strategies**: Estratégias inteligentes para dividir documentos
- **Metadata Filtering**: Filtrar documentos antes da busca por similaridade
- **Re-ranking**: Reordenar resultados por relevância mais precisa

### Benefícios:

- **Precisão**: Resultados mais relevantes
- **Performance**: Busca mais rápida com filtros
- **Qualidade**: Re-ranking melhora ordenação final

---

## Chunking Strategies

### 1. **Chunking por Tamanho**

Dividir documento em chunks de tamanho fixo com overlap.

**Exemplo:**
```python
def chunk_por_tamanho(texto, tamanho=1000, overlap=200):
    chunks = []
    inicio = 0
    while inicio < len(texto):
        fim = inicio + tamanho
        chunk = texto[inicio:fim]
        chunks.append(chunk)
        inicio = fim - overlap
    return chunks
```

**Quando usar:**
- Documentos uniformes
- Texto simples sem estrutura

### 2. **Chunking Hierárquico**

Dividir por estrutura do documento (parágrafos, seções).

**Exemplo:**
```python
def chunk_hierarquico(texto):
    # Dividir por parágrafos
    paragrafos = texto.split('\n\n')
    chunks = []
    for paragrafo in paragrafos:
        if len(paragrafo) > 100:  # Mínimo de caracteres
            chunks.append(paragrafo)
    return chunks
```

**Quando usar:**
- Documentos estruturados
- Preservar contexto de parágrafos

### 3. **Chunking Semântico**

Dividir por similaridade semântica usando embeddings.

**Quando usar:**
- Preservar significado completo
- Documentos complexos

---

## Metadata Filtering

### 1. **Filtrar por Tipo**

Buscar apenas em documentos de tipo específico.

**Exemplo:**
```python
results = client.search(
    collection_name="documentos",
    query_vector=query_embedding,
    query_filter=Filter(
        must=[FieldCondition(key="tipo", match=MatchValue(value="manual"))]
    ),
    limit=5
)
```

### 2. **Filtrar por Data**

Buscar apenas documentos recentes.

**Exemplo:**
```python
results = client.search(
    collection_name="documentos",
    query_vector=query_embedding,
    query_filter=Filter(
        must=[FieldCondition(key="data", range=Range(gte="2024-01-01"))]
    ),
    limit=5
)
```

### 3. **Filtrar por Fonte**

Buscar apenas de fonte específica.

**Exemplo:**
```python
results = client.search(
    collection_name="documentos",
    query_vector=query_embedding,
    query_filter=Filter(
        must=[FieldCondition(key="fonte", match=MatchValue(value="documentacao"))]
    ),
    limit=5
)
```

---

## Re-ranking

### 1. **Cross-Encoder**

Usar modelo que compara query e documento juntos.

**Exemplo:**
```python
from sentence_transformers import CrossEncoder

cross_encoder = CrossEncoder('cross-encoder/ms-marco-MiniLM-L-6-v2')

def rerank(query, documentos):
    pairs = [[query, doc] for doc in documentos]
    scores = cross_encoder.predict(pairs)
    
    # Ordenar por score
    ranked = sorted(zip(documentos, scores), key=lambda x: x[1], reverse=True)
    return ranked
```

### 2. **LLM-based Re-ranking**

Usar LLM para reordenar resultados.

**Exemplo:**
```python
def rerank_com_llm(query, documentos):
    prompt = f"""Ordene os documentos por relevância para a pergunta:

Pergunta: {query}

Documentos:
{formatar_documentos(documentos)}

Retorne apenas números dos documentos em ordem de relevância:"""
    
    resposta = chamar_llm(prompt)
    return parsear_ordem(resposta)
```

---

## Melhores Práticas

1. **Chunking**: Ajuste tamanho e overlap para tipo de documento
2. **Metadata**: Adicione metadados úteis durante indexação
3. **Filtros**: Use filtros para reduzir espaço de busca
4. **Re-ranking**: Use para melhorar top N resultados finais
5. **Performance**: Balance precisão vs latência

## Casos de Uso Comuns

- **Documentos longos**: Chunking hierárquico preserva contexto
- **Múltiplos tipos**: Metadata filtering separa por categoria
- **Relevância crítica**: Re-ranking melhora ordenação final

## Próximos Passos

Após entender técnicas avançadas, você estará pronto para:
- Otimizar chunking para seu caso de uso
- Implementar filtros baseados em metadados
- Adicionar re-ranking ao pipeline RAG
- Avançar para otimização de produção no próximo módulo

---

**Recursos Oficiais:**

- [LangChain Chunking Strategies](https://python.langchain.com/docs/modules/data_connection/document_transformers/)
- [Sentence Transformers Re-ranking](https://www.sbert.net/examples/applications/cross-encoder/README.html)
- [Qdrant Filtering](https://qdrant.tech/documentation/concepts/filtering/)
