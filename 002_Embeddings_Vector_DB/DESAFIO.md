# Desafio: Sistema de Busca por Similaridade com Embeddings e Qdrant

## Índice

- [Objetivo](#objetivo)
- [Pré-requisitos](#pré-requisitos)
- [Cenário](#cenário)
- [Tasks](#tasks)
  - [Task 1: Configurar Qdrant](#task-1-configurar-qdrant)
  - [Task 2: Instalar Dependências](#task-2-instalar-dependências)
  - [Task 3: Criar Embeddings de Documentos](#task-3-criar-embeddings-de-documentos)
  - [Task 4: Armazenar Embeddings no Qdrant](#task-4-armazenar-embeddings-no-qdrant)
  - [Task 5: Implementar Busca por Similaridade](#task-5-implementar-busca-por-similaridade)
  - [Task 6: Adicionar Metadados e Filtros](#task-6-adicionar-metadados-e-filtros)
  - [Task 7: Testar Sistema Completo](#task-7-testar-sistema-completo)
  - [Task 8: Integrar Busca com LLM](#task-8-integrar-busca-com-llm)
- [Resultados Esperados Finais](#resultados-esperados-finais)
- [Checklist de Validação](#checklist-de-validação)
- [Dicas de Troubleshooting](#dicas-de-troubleshooting)
- [Extras (Opcional)](#extras-opcional)
- [Próximos Passos](#próximos-passos)

---

## Objetivo

Implementar um sistema completo de busca por similaridade usando embeddings da OpenAI e Qdrant como vector database, incluindo armazenamento, busca, filtros e integração com LLM.

## Pré-requisitos

- Python 3.8+ instalado
- Docker instalado (para Qdrant) ou acesso ao Qdrant Cloud
- Conta OpenAI (para embeddings)
- Completou módulo 001 (Python, APIs LLM)
- Conhecimento básico de Docker (opcional)

## Cenário

Você está criando um sistema de busca semântica para uma biblioteca de documentos técnicos. O sistema deve encontrar documentos similares baseado no significado, não apenas palavras-chave exatas.

## Tasks

### Task 1: Configurar Qdrant

**Descrição:** Configurar Qdrant localmente usando Docker ou usar Qdrant Cloud.

**Requisitos:**
- Docker instalado (para Qdrant local)
- Ou conta Qdrant Cloud (alternativa)

**Ações:**

**Opção A: Qdrant Local (Docker)**
```bash
# Pull da imagem Qdrant
docker pull qdrant/qdrant

# Iniciar Qdrant local
docker run -p 6333:6333 -p 6334:6334 qdrant/qdrant

# Verificar se está rodando
curl http://localhost:6333/health
```

**Opção B: Qdrant Cloud (Alternativa)**
1. Criar conta em https://cloud.qdrant.io/
2. Criar cluster gratuito
3. Obter URL e API key

**Resultado Esperado:**
- Qdrant rodando em `http://localhost:6333` (local) ou URL do cloud
- Health check retorna status OK
- Interface web acessível em `http://localhost:6333/dashboard` (local)

---

### Task 2: Instalar Dependências

**Descrição:** Instalar bibliotecas necessárias para trabalhar com embeddings e Qdrant.

**Requisitos:**
- Biblioteca OpenAI (para embeddings)
- Qdrant Python client
- python-dotenv (para variáveis de ambiente)

**Ações:**
1. Adicionar ao `requirements.txt`:
   ```
   openai>=1.0.0
   qdrant-client>=1.7.0
   python-dotenv>=1.0.0
   ```
2. Instalar:
   ```bash
   pip install -r requirements.txt
   ```
3. Verificar instalação:
   ```bash
   pip list | grep qdrant
   ```

**Resultado Esperado:**
- `qdrant-client` instalado
- `openai` instalado (do módulo anterior)
- Comando `pip list` mostra as bibliotecas

---

### Task 3: Criar Embeddings de Documentos

**Descrição:** Criar função que converte textos em embeddings usando OpenAI.

**Requisitos:**
- Função para criar embedding de um texto
- Função para criar embeddings em batch (múltiplos textos)
- Tratamento de erros básico

**Ações:**
1. Criar arquivo `embedding_helper.py`:
   ```python
   import os
   from dotenv import load_dotenv
   from openai import OpenAI

   load_dotenv()
   client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

   def criar_embedding(texto):
       """Cria embedding de um texto usando OpenAI."""
       try:
           response = client.embeddings.create(
               model="text-embedding-ada-002",
               input=texto
           )
           return response.data[0].embedding
       except Exception as e:
           print(f"Erro ao criar embedding: {e}")
           return None

   def criar_embeddings_batch(textos):
       """Cria embeddings de múltiplos textos."""
       embeddings = []
       for texto in textos:
           embedding = criar_embedding(texto)
           if embedding:
               embeddings.append(embedding)
       return embeddings

   # Testar
   if __name__ == "__main__":
       texto_teste = "Machine learning é uma área da inteligência artificial"
       embedding = criar_embedding(texto_teste)
       print(f"Dimensão do embedding: {len(embedding)}")
       print(f"Primeiros 5 valores: {embedding[:5]}")
   ```
2. Executar teste:
   ```bash
   python embedding_helper.py
   ```

**Resultado Esperado:**
- Função `criar_embedding` retorna vetor de 1536 dimensões
- Função `criar_embeddings_batch` processa múltiplos textos
- Teste executa sem erros
- Embedding tem dimensão 1536 (modelo ada-002)

---

### Task 4: Armazenar Embeddings no Qdrant

**Descrição:** Conectar ao Qdrant, criar collection e armazenar embeddings com metadados.

**Requisitos:**
- Conectar ao Qdrant
- Criar collection com dimensão 1536
- Armazenar embeddings com payload (texto original, metadados)

**Ações:**
1. Criar arquivo `qdrant_setup.py`:
   ```python
   from qdrant_client import QdrantClient
   from qdrant_client.models import Distance, VectorParams, PointStruct
   from embedding_helper import criar_embedding

   # Conectar ao Qdrant (local)
   client = QdrantClient("localhost", port=6333)
   # Ou para Qdrant Cloud:
   # client = QdrantClient(url="https://...", api_key="...")

   # Criar collection
   collection_name = "documentos"
   
   try:
       # Verificar se collection já existe
       collections = client.get_collections()
       collection_exists = any(c.name == collection_name for c in collections.collections)
       
       if not collection_exists:
           client.create_collection(
               collection_name=collection_name,
               vectors_config=VectorParams(size=1536, distance=Distance.COSINE)
           )
           print(f"✅ Collection '{collection_name}' criada!")
       else:
           print(f"ℹ️ Collection '{collection_name}' já existe.")
   except Exception as e:
       print(f"Erro ao criar collection: {e}")

   # Documentos de exemplo
   documentos = [
       {
           "id": 1,
           "texto": "Machine learning é uma área da inteligência artificial que permite sistemas aprenderem sem programação explícita.",
           "categoria": "tecnologia",
           "autor": "João Silva"
       },
       {
           "id": 2,
           "texto": "Python é uma linguagem de programação popular para ciência de dados e machine learning.",
           "categoria": "programação",
           "autor": "Maria Santos"
       },
       {
           "id": 3,
           "texto": "Neural networks são modelos inspirados no cérebro humano para processar informações complexas.",
           "categoria": "tecnologia",
           "autor": "João Silva"
       },
       {
           "id": 4,
           "texto": "APIs REST permitem comunicação entre diferentes sistemas usando protocolo HTTP.",
           "categoria": "programação",
           "autor": "Pedro Costa"
       }
   ]

   # Criar embeddings e armazenar
   points = []
   for doc in documentos:
       embedding = criar_embedding(doc["texto"])
       if embedding:
           points.append(
               PointStruct(
                   id=doc["id"],
                   vector=embedding,
                   payload={
                       "texto": doc["texto"],
                       "categoria": doc["categoria"],
                       "autor": doc["autor"]
                   }
               )
           )

   # Inserir no Qdrant
   if points:
       client.upsert(collection_name=collection_name, points=points)
       print(f"✅ {len(points)} documentos inseridos no Qdrant!")
   ```
2. Executar:
   ```bash
   python qdrant_setup.py
   ```

**Resultado Esperado:**
- Collection criada no Qdrant
- 4 documentos inseridos com embeddings
- Cada documento tem payload com texto, categoria e autor
- Console mostra mensagem de sucesso

---

### Task 5: Implementar Busca por Similaridade

**Descrição:** Criar função que busca documentos similares usando embedding da query.

**Requisitos:**
- Função que recebe query de texto
- Cria embedding da query
- Busca documentos similares no Qdrant
- Retorna top N resultados com scores

**Ações:**
1. Criar arquivo `busca_similaridade.py`:
   ```python
   from qdrant_client import QdrantClient
   from embedding_helper import criar_embedding

   client = QdrantClient("localhost", port=6333)
   collection_name = "documentos"

   def buscar_similares(query, limite=3):
       """Busca documentos similares à query."""
       # Criar embedding da query
       query_embedding = criar_embedding(query)
       
       if not query_embedding:
           return []
       
       # Buscar no Qdrant
       results = client.search(
           collection_name=collection_name,
           query_vector=query_embedding,
           limit=limite
       )
       
       return results

   # Testar
   if __name__ == "__main__":
       queries_teste = [
           "Como funciona inteligência artificial?",
           "Quais são as linguagens de programação para dados?",
           "O que são redes neurais?"
       ]
       
       for query in queries_teste:
           print(f"\n🔍 Query: {query}")
           print("-" * 60)
           resultados = buscar_similares(query, limite=2)
           
           for i, result in enumerate(resultados, 1):
               print(f"\n[{i}] Score: {result.score:.3f}")
               print(f"Texto: {result.payload['texto']}")
               print(f"Categoria: {result.payload['categoria']}")
   ```
2. Executar e analisar resultados

**Resultado Esperado:**
- Função `buscar_similares` retorna resultados ordenados por score
- Scores mais altos = documentos mais similares
- Documentos retornados são semanticamente relevantes à query
- Metadados (categoria, autor) estão presentes nos resultados

---

### Task 6: Adicionar Metadados e Filtros

**Descrição:** Implementar busca com filtros usando metadados (payload).

**Requisitos:**
- Buscar apenas documentos de categoria específica
- Filtrar por autor
- Combinar similarity search com filtros

**Ações:**
1. Criar arquivo `busca_com_filtros.py`:
   ```python
   from qdrant_client import QdrantClient
   from qdrant_client.models import Filter, FieldCondition, MatchValue
   from embedding_helper import criar_embedding

   client = QdrantClient("localhost", port=6333)
   collection_name = "documentos"

   def buscar_com_filtro(query, categoria=None, autor=None, limite=3):
       """Busca documentos similares com filtros opcionais."""
       query_embedding = criar_embedding(query)
       
       if not query_embedding:
           return []
       
       # Construir filtro
       filters = []
       if categoria:
           filters.append(FieldCondition(key="categoria", match=MatchValue(value=categoria)))
       if autor:
           filters.append(FieldCondition(key="autor", match=MatchValue(value=autor)))
       
       query_filter = Filter(must=filters) if filters else None
       
       # Buscar
       results = client.search(
           collection_name=collection_name,
           query_vector=query_embedding,
           query_filter=query_filter,
           limit=limite
       )
       
       return results

   # Testar
   if __name__ == "__main__":
       query = "Como funciona inteligência artificial?"
       
       print("🔍 Busca sem filtro:")
       resultados = buscar_com_filtro(query)
       for r in resultados:
           print(f"  - {r.payload['categoria']}: {r.payload['texto'][:50]}...")
       
       print("\n🔍 Busca apenas 'tecnologia':")
       resultados = buscar_com_filtro(query, categoria="tecnologia")
       for r in resultados:
           print(f"  - {r.payload['categoria']}: {r.payload['texto'][:50]}...")
       
       print("\n🔍 Busca apenas 'programação':")
       resultados = buscar_com_filtro(query, categoria="programação")
       for r in resultados:
           print(f"  - {r.payload['categoria']}: {r.payload['texto'][:50]}...")
   ```
2. Executar e verificar filtros funcionando

**Resultado Esperado:**
- Busca sem filtro retorna documentos de todas as categorias
- Busca com `categoria="tecnologia"` retorna apenas documentos dessa categoria
- Busca com `categoria="programação"` retorna apenas dessa categoria
- Filtros não afetam a ordenação por similaridade

---

### Task 7: Testar Sistema Completo

**Descrição:** Testar o sistema completo com diferentes queries e analisar resultados.

**Requisitos:**
- Testar com queries semânticas (sem palavras-chave exatas)
- Verificar que resultados são relevantes
- Analisar scores e similaridade

**Ações:**
1. Criar arquivo `teste_completo.py`:
   ```python
   from busca_similaridade import buscar_similares

   def teste_sistema():
       """Testa sistema com diferentes cenários."""
       
       testes = [
           {
               "query": "IA e aprendizado de máquina",
               "esperado": "Documentos sobre machine learning e IA"
           },
           {
               "query": "linguagens para ciência de dados",
               "esperado": "Documento sobre Python"
           },
           {
               "query": "modelos inspirados no cérebro",
               "esperado": "Documento sobre neural networks"
           },
           {
               "query": "comunicação entre sistemas",
               "esperado": "Documento sobre APIs REST"
           }
       ]
       
       print("🧪 Testando Sistema de Busca por Similaridade\n")
       print("=" * 70)
       
       for i, teste in enumerate(testes, 1):
           query = teste["query"]
           esperado = teste["esperado"]
           
           print(f"\nTeste {i}:")
           print(f"Query: {query}")
           print(f"Esperado: {esperado}")
           print("-" * 70)
           
           resultados = buscar_similares(query, limite=2)
           
           if resultados:
               for j, result in enumerate(resultados, 1):
                   print(f"\n[{j}] Score: {result.score:.4f}")
                   print(f"Texto: {result.payload['texto']}")
           else:
               print("❌ Nenhum resultado encontrado")
           
           print("\n" + "=" * 70)

   if __name__ == "__main__":
       teste_sistema()
   ```
2. Executar e analisar se resultados fazem sentido

**Resultado Esperado:**
- Todas as queries retornam resultados relevantes
- Queries semânticas (sem palavras exatas) ainda funcionam
- Scores indicam nível de similaridade
- Documentos retornados são semanticamente relacionados à query

---

### Task 8: Integrar Busca com LLM

**Descrição:** Integrar busca por similaridade com LLM para criar resposta baseada em contexto.

**Requisitos:**
- Buscar documentos similares
- Usar documentos como contexto para LLM
- Gerar resposta baseada no contexto

**Ações:**
1. Criar arquivo `rag_simples.py`:
   ```python
   from busca_similaridade import buscar_similares
   from llm_helper import chamar_llm  # Do módulo anterior

   def responder_com_contexto(query, limite_docs=3):
       """Busca documentos similares e usa como contexto para LLM."""
       
       # 1. Buscar documentos similares
       resultados = buscar_similares(query, limite=limite_docs)
       
       if not resultados:
           return "Desculpe, não encontrei documentos relevantes."
       
       # 2. Extrair textos relevantes
       textos_contexto = [r.payload["texto"] for r in resultados]
       contexto = "\n\n".join(textos_contexto)
       
       # 3. Criar prompt para LLM
       prompt = f"""Baseado no contexto abaixo, responda a pergunta do usuário.
       
   Se a resposta não estiver no contexto, diga "Não encontrei informações suficientes no contexto."

   Contexto:
   {contexto}

   Pergunta: {query}
   
   Resposta:"""
       
       # 4. Chamar LLM
       resultado = chamar_llm(
           mensagem_usuario=prompt,
           system_prompt="Você é um assistente que responde perguntas baseado no contexto fornecido.",
           temperatura=0.5
       )
       
       return resultado["content"]

   # Testar
   if __name__ == "__main__":
       queries = [
           "Como funciona machine learning?",
           "Qual linguagem é usada para ciência de dados?",
           "O que são neural networks?"
       ]
       
       for query in queries:
           print(f"\n❓ Pergunta: {query}")
           print("-" * 60)
           resposta = responder_com_contexto(query)
           print(f"💬 Resposta: {resposta}\n")
   ```
2. Executar e verificar que respostas usam contexto

**Resultado Esperado:**
- Sistema busca documentos relevantes
- LLM usa contexto para gerar resposta
- Respostas são mais precisas e contextualizadas
- Sistema funciona como RAG básico

---

## Resultados Esperados Finais

Ao final do desafio, você deve ter:

✅ **Qdrant configurado** e funcionando (local ou cloud)  
✅ **Função para criar embeddings** usando OpenAI  
✅ **Collection criada** no Qdrant com documentos  
✅ **Busca por similaridade funcionando** retornando documentos relevantes  
✅ **Filtros por metadados implementados** (categoria, autor)  
✅ **Sistema completo testado** com diferentes queries  
✅ **Integração RAG básica** funcionando (busca + LLM)  
✅ **Entendimento prático** de embeddings e vector databases  

## Checklist de Validação

- [ ] Qdrant rodando e acessível (local ou cloud)
- [ ] `qdrant-client` instalado e funcionando
- [ ] Função `criar_embedding` retorna vetores de 1536 dimensões
- [ ] Collection criada no Qdrant com dimensão correta
- [ ] Documentos inseridos com embeddings e payload
- [ ] Busca por similaridade retorna resultados ordenados por score
- [ ] Filtros por categoria funcionam corretamente
- [ ] Sistema completo testado com diferentes queries
- [ ] Integração RAG básica funcionando
- [ ] Resultados são semanticamente relevantes (não apenas palavras-chave)

## Dicas de Troubleshooting

1. **Qdrant não conecta?**
   - Verifique se está rodando: `curl http://localhost:6333/health`
   - Verifique porta (6333 para HTTP, 6334 para gRPC)
   - Se usar Docker, verifique se container está rodando: `docker ps`

2. **Erro ao criar collection?**
   - Verifique dimensão do embedding (deve ser 1536 para ada-002)
   - Verifique se collection já existe antes de criar
   - Verifique permissões de acesso ao Qdrant

3. **Busca retorna vazio?**
   - Verifique se documentos foram inseridos: `client.count(collection_name="documentos")`
   - Verifique se embedding da query está correto (1536 dimensões)
   - Verifique se collection name está correto

4. **Scores muito baixos?**
   - Isso pode ser normal dependendo do conteúdo
   - Verifique se embeddings estão sendo criados corretamente
   - Teste com queries mais específicas

5. **Qdrant Cloud (alternativa):**
   ```python
   from qdrant_client import QdrantClient
   
   # Usar URL e API key do Qdrant Cloud
   client = QdrantClient(
       url="https://xxxxx.us-east-1-0.aws.cloud.qdrant.io:6333",
       api_key="sua-api-key"
   )
   ```

## Extras (Opcional)

Se quiser ir além:

- Implementar batch upsert para inserir muitos documentos
- Adicionar mais metadados (data, tags, source)
- Implementar re-ranking dos resultados
- Criar interface web para testes
- Adicionar persistência de embeddings (evitar recriar)
- Implementar chunking de documentos longos antes de criar embeddings
- Medir performance (tempo de busca, throughput)
- Comparar diferentes modelos de embedding

## Próximos Passos

Após completar este desafio, você estará pronto para:

- Construir sistema RAG completo com upload de documentos
- Implementar chunking strategies para documentos longos
- Adicionar re-ranking para melhorar resultados
- Integrar com backend Node.js
- Otimizar performance e custos
- Avançar para arquitetura RAG completa no próximo módulo

---

**Tempo estimado:** 3-4 horas  
**Dificuldade:** Intermediária  
**Importância:** ⭐⭐⭐⭐⭐ (Fundamental para RAG)
