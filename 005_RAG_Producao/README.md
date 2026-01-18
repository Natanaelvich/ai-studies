# RAG em Produção - Cache, Performance e Avaliação

## Índice

- [O que é RAG em Produção?](#o-que-é-rag-em-produção)
- [Cache de Respostas](#cache-de-respostas)
  - [1. Cache Simples](#1-cache-simples)
  - [2. Cache Inteligente](#2-cache-inteligente)
  - [3. Redis Cache](#3-redis-cache)
- [Performance](#performance)
  - [1. Latência](#1-latência)
  - [2. Throughput](#2-throughput)
  - [3. Otimização](#3-otimização)
- [Custos](#custos)
  - [1. Token Usage](#1-token-usage)
  - [2. Otimização de Custos](#2-otimização-de-custos)
- [Avaliação de RAG](#avaliação-de-rag)
  - [1. Métricas](#1-métricas)
  - [2. Testes](#2-testes)
- [Melhores Práticas](#melhores-práticas)
- [Casos de Uso Comuns](#casos-de-uso-comuns)
- [Próximos Passos](#próximos-passos)

---

## O que é RAG em Produção?

RAG em produção requer atenção a:
- **Cache**: Reduzir chamadas desnecessárias à API
- **Performance**: Latência e throughput aceitáveis
- **Custos**: Monitorar e otimizar uso de tokens
- **Avaliação**: Medir qualidade do sistema

### Desafios:

- **Latência**: Respostas rápidas (< 2s)
- **Custos**: Controlar gastos com APIs
- **Qualidade**: Manter precisão em escala
- **Escalabilidade**: Suportar muitos usuários

---

## Cache de Respostas

### 1. **Cache Simples**

Cache em memória para respostas frequentes.

**Exemplo:**
```python
cache = {}

def chat_com_cache(query):
    # Verificar cache
    if query in cache:
        return cache[query]
    
    # Gerar resposta
    resposta = chat(query)
    
    # Armazenar no cache
    cache[query] = resposta
    return resposta
```

### 2. **Cache Inteligente**

Cache baseado em similaridade semântica.

**Exemplo:**
```python
def chat_cache_inteligente(query, threshold=0.9):
    # Buscar no cache por similaridade
    for cached_query, resposta in cache.items():
        similaridade = calcular_similaridade(query, cached_query)
        if similaridade >= threshold:
            return resposta
    
    # Gerar nova resposta
    resposta = chat(query)
    cache[query] = resposta
    return resposta
```

### 3. **Redis Cache**

Cache persistente usando Redis.

**Exemplo:**
```python
import redis
import json

redis_client = redis.Redis(host='localhost', port=6379)

def chat_com_redis(query, ttl=3600):
    # Verificar Redis
    cached = redis_client.get(query)
    if cached:
        return json.loads(cached)
    
    # Gerar resposta
    resposta = chat(query)
    
    # Armazenar no Redis
    redis_client.setex(query, ttl, json.dumps(resposta))
    return resposta
```

---

## Performance

### 1. **Latência**

Medir tempo de resposta do sistema.

**Métricas:**
- Tempo total de resposta
- Tempo de busca no vector DB
- Tempo de geração do LLM

**Otimização:**
- Usar cache para queries frequentes
- Paralelizar buscas quando possível
- Reduzir tamanho do contexto

### 2. **Throughput**

Número de requisições por segundo.

**Métricas:**
- Requisições por segundo (RPS)
- Processamento paralelo
- Fila de requisições

**Otimização:**
- Processamento assíncrono
- Batch operations quando possível
- Balanceamento de carga

### 3. **Otimização**

Estratégias para melhorar performance.

**Técnicas:**
- Reduzir número de documentos no contexto
- Usar filtros para reduzir espaço de busca
- Otimizar tamanho dos chunks
- Usar modelos mais rápidos quando apropriado

---

## Custos

### 1. **Token Usage**

Monitorar uso de tokens em cada chamada.

**Exemplo:**
```python
def chat_com_metricas(query):
    resposta = chamar_llm(query)
    
    # Capturar tokens usados
    tokens_input = resposta.usage.prompt_tokens
    tokens_output = resposta.usage.completion_tokens
    tokens_total = resposta.usage.total_tokens
    
    # Calcular custo
    custo = calcular_custo(tokens_input, tokens_output)
    
    return resposta, {"tokens": tokens_total, "custo": custo}
```

### 2. **Otimização de Custos**

Estratégias para reduzir custos.

**Técnicas:**
- Usar cache para evitar chamadas duplicadas
- Reduzir tamanho do contexto
- Usar modelos mais baratos quando apropriado
- Batch embeddings quando possível

---

## Avaliação de RAG

### 1. **Métricas**

Métricas para avaliar qualidade do sistema.

**Métricas Principais:**
- **Precision**: Relevância dos documentos recuperados
- **Recall**: Cobertura de documentos relevantes
- **F1-Score**: Balance entre precision e recall
- **Relevância semântica**: Similaridade entre query e documentos

### 2. **Testes**

Criar testes automatizados para avaliar qualidade.

**Exemplo:**
```python
def avaliar_rag(query_esperado_pares):
    scores = []
    for query, documento_esperado in query_esperado_pares:
        # Buscar documentos
        resultados = buscar_similares(query)
        
        # Calcular relevância
        relevancia = calcular_relevancia(resultados, documento_esperado)
        scores.append(relevancia)
    
    # Calcular média
    score_medio = sum(scores) / len(scores)
    return score_medio
```

---

## Melhores Práticas

1. **Cache**: Use cache para queries frequentes
2. **Performance**: Monitore latência e throughput
3. **Custos**: Rastreie uso de tokens
4. **Avaliação**: Crie testes automatizados
5. **Métricas**: Meça qualidade continuamente

## Casos de Uso Comuns

- **Cache**: Reduzir custos e latência
- **Performance**: Suportar muitos usuários
- **Custos**: Controlar gastos com APIs
- **Avaliação**: Garantir qualidade

## Próximos Passos

Após entender produção RAG, você estará pronto para:
- Implementar sistema multi-usuário
- Adicionar controle de acesso
- Implementar logs e observabilidade
- Avançar para sistema corporativo no próximo módulo

---

**Recursos Oficiais:**

- [OpenAI Token Usage](https://platform.openai.com/docs/guides/pricing)
- [Redis Documentation](https://redis.io/docs/)
- [RAG Evaluation](https://python.langchain.com/docs/guides/evaluation/)
