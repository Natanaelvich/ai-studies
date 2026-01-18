# Desafio: Chatbot RAG Completo com Upload de PDFs

## Índice

- [Objetivo](#objetivo)
- [Pré-requisitos](#pré-requisitos)
- [Cenário](#cenário)
- [Tasks](#tasks)
  - [Task 1: Estrutura do Projeto](#task-1-estrutura-do-projeto)
  - [Task 2: Processar PDFs](#task-2-processar-pdfs)
  - [Task 3: Chunking de Textos](#task-3-chunking-de-textos)
  - [Task 4: Indexar Documentos](#task-4-indexar-documentos)
  - [Task 5: API de Chat](#task-5-api-de-chat)
  - [Task 6: Histórico de Conversa](#task-6-histórico-de-conversa)
  - [Task 7: Integração Backend](#task-7-integração-backend)
  - [Task 8: Testar Sistema Completo](#task-8-testar-sistema-completo)
- [Resultados Esperados Finais](#resultados-esperados-finais)
- [Checklist de Validação](#checklist-de-validação)
- [Dicas de Troubleshooting](#dicas-de-troubleshooting)
- [Extras (Opcional)](#extras-opcional)
- [Próximos Passos](#próximos-passos)

---

## Objetivo

Implementar um chatbot RAG completo que permite upload de PDFs, processamento, indexação e chat com histórico, seguindo a arquitetura RAG completa (retriever + generator).

## Pré-requisitos

- Python 3.8+ instalado
- Node.js 18+ instalado (para backend)
- Qdrant rodando (local ou cloud)
- Conta OpenAI (para embeddings e LLM)
- Completou módulos 001 e 002
- Conhecimento básico de APIs REST

## Cenário

Você está criando um chatbot para uma empresa que precisa responder perguntas sobre documentos técnicos. O sistema deve permitir upload de PDFs, processá-los automaticamente e permitir chat com histórico.

## Tasks

### Task 1: Estrutura do Projeto

**Descrição:** Criar estrutura de diretórios para backend Node e serviço Python.

**Requisitos:**
- Diretório `backend/` (Node.js)
- Diretório `python-service/` (Python)
- Arquivo `requirements.txt` (Python)
- Arquivo `package.json` (Node.js)

**Ações:**
1. Criar estrutura:
   ```
   projeto/
   ├── backend/
   │   ├── src/
   │   ├── package.json
   │   └── .env
   ├── python-service/
   │   ├── services/
   │   ├── requirements.txt
   │   └── .env
   └── uploads/
   ```
2. Configurar `package.json`:
   ```json
   {
     "name": "rag-chatbot-backend",
     "version": "1.0.0",
     "scripts": {
       "start": "node src/index.js",
       "dev": "nodemon src/index.js"
     },
     "dependencies": {
       "express": "^4.18.0",
       "multer": "^1.4.5",
       "axios": "^1.6.0",
       "dotenv": "^16.3.0"
     }
   }
   ```

**Resultado Esperado:**
- Estrutura de diretórios criada
- `package.json` e `requirements.txt` configurados
- Diretório `uploads/` para PDFs

---

### Task 2: Processar PDFs

**Descrição:** Criar serviço Python para extrair texto de PDFs.

**Ações:**
1. Instalar dependências:
   ```bash
   pip install PyPDF2 python-dotenv
   ```
2. Criar `python-service/pdf_processor.py`:
   ```python
   import PyPDF2
   import os

   def extrair_texto_pdf(caminho_pdf):
       with open(caminho_pdf, 'rb') as arquivo:
           leitor = PyPDF2.PdfReader(arquivo)
           texto = ""
           for pagina in leitor.pages:
               texto += pagina.extract_text()
       return texto
   ```

**Resultado Esperado:**
- Função que extrai texto de PDFs
- Teste com PDF de exemplo funcionando

---

### Task 3: Chunking de Textos

**Descrição:** Implementar chunking para quebrar textos longos em pedaços.

**Ações:**
1. Criar `python-service/chunking.py`:
   ```python
   def chunk_texto(texto, tamanho_chunk=1000, overlap=200):
       chunks = []
       inicio = 0
       
       while inicio < len(texto):
           fim = inicio + tamanho_chunk
           chunk = texto[inicio:fim]
           chunks.append(chunk)
           inicio = fim - overlap
       
       return chunks
   ```

**Resultado Esperado:**
- Função de chunking criada
- Chunks têm tamanho consistente com overlap

---

### Task 4: Indexar Documentos

**Descrição:** Criar endpoint Python que processa PDF, faz chunking, cria embeddings e armazena no Qdrant.

**Ações:**
1. Criar `python-service/indexer.py`:
   ```python
   from pdf_processor import extrair_texto_pdf
   from chunking import chunk_texto
   from embedding_helper import criar_embedding
   from qdrant_client import QdrantClient
   from qdrant_client.models import PointStruct

   client = QdrantClient("localhost", port=6333)

   def indexar_documento(caminho_pdf, doc_id):
       # 1. Extrair texto
       texto = extrair_texto_pdf(caminho_pdf)
       
       # 2. Chunking
       chunks = chunk_texto(texto)
       
       # 3. Criar embeddings
       embeddings = [criar_embedding(chunk) for chunk in chunks]
       
       # 4. Armazenar no Qdrant
       points = []
       for i, (chunk, embedding) in enumerate(zip(chunks, embeddings)):
           points.append(
               PointStruct(
                   id=f"{doc_id}_{i}",
                   vector=embedding,
                   payload={"texto": chunk, "documento_id": doc_id}
               )
           )
       
       client.upsert(collection_name="documentos", points=points)
       return len(points)
   ```

**Resultado Esperado:**
- Função `indexar_documento` criada
- PDFs processados e indexados no Qdrant
- Chunks armazenados com metadados

---

### Task 5: API de Chat

**Descrição:** Criar endpoint de chat que busca documentos relevantes e gera resposta.

**Ações:**
1. Criar `python-service/chat_service.py`:
   ```python
   from embedding_helper import criar_embedding
   from qdrant_client import QdrantClient
   from llm_helper import chamar_llm

   client = QdrantClient("localhost", port=6333)

   def chat(query):
       # 1. Buscar documentos similares
       query_embedding = criar_embedding(query)
       resultados = client.search(
           collection_name="documentos",
           query_vector=query_embedding,
           limit=3
       )
       
       # 2. Criar contexto
       contexto = "\n".join([r.payload["texto"] for r in resultados])
       
       # 3. Gerar resposta
       prompt = f"""Contexto:
   {contexto}

   Pergunta: {query}
   Resposta:"""
       
       resposta = chamar_llm(prompt, temperatura=0.7)
       return resposta["content"]
   ```

**Resultado Esperado:**
- Endpoint de chat funcionando
- Respostas baseadas em documentos indexados
- Contexto relevante usado na geração

---

### Task 6: Histórico de Conversa

**Descrição:** Implementar histórico de conversa para manter contexto.

**Ações:**
1. Atualizar `chat_service.py`:
   ```python
   historico_global = {}  # {session_id: [mensagens]}

   def chat_com_historico(query, session_id="default"):
       if session_id not in historico_global:
           historico_global[session_id] = []
       
       # Buscar documentos e gerar resposta (similar ao Task 5)
       # Adicionar mensagens ao histórico
       historico_global[session_id].append({"role": "user", "content": query})
       historico_global[session_id].append({"role": "assistant", "content": resposta})
       
       return resposta
   ```

**Resultado Esperado:**
- Histórico mantido por sessão
- Contexto de conversas anteriores usado
- Limite de histórico implementado (últimas N mensagens)

---

### Task 7: Integração Backend

**Descrição:** Criar backend Node.js que integra com serviço Python.

**Ações:**
1. Criar `backend/src/index.js`:
   ```javascript
   const express = require('express');
   const multer = require('multer');
   const axios = require('axios');
   const path = require('path');
   const fs = require('fs');

   const app = express();
   const upload = multer({ dest: 'uploads/' });

   // Upload de PDF
   app.post('/upload', upload.single('pdf'), async (req, res) => {
     const caminho = req.file.path;
     // Chamar serviço Python para indexar
     const response = await axios.post('http://localhost:5000/index', {
       caminho_pdf: caminho
     });
     res.json({ success: true, doc_id: response.data.doc_id });
   });

   // Chat
   app.post('/chat', async (req, res) => {
     const { query, session_id } = req.body;
     const response = await axios.post('http://localhost:5000/chat', {
       query, session_id
     });
     res.json({ resposta: response.data.resposta });
   });

   app.listen(3000, () => console.log('Backend rodando na porta 3000'));
   ```

**Resultado Esperado:**
- Backend Node.js criado
- Endpoint `/upload` para PDFs
- Endpoint `/chat` para conversas
- Integração com serviço Python funcionando

---

### Task 8: Testar Sistema Completo

**Descrição:** Testar fluxo completo: upload → indexação → chat → histórico.

**Ações:**
1. Fazer upload de PDF de teste
2. Verificar indexação no Qdrant
3. Fazer perguntas sobre o documento
4. Verificar histórico de conversa
5. Testar com múltiplos PDFs

**Resultado Esperado:**
- Upload de PDFs funcionando
- Documentos indexados corretamente
- Chat retorna respostas relevantes
- Histórico mantido por sessão
- Sistema completo funcional

---

## Resultados Esperados Finais

✅ **Sistema completo de chatbot RAG** funcionando  
✅ **Upload de PDFs** processado e indexado  
✅ **Chat com histórico** mantendo contexto  
✅ **Backend Node + Python** integrados  
✅ **Documentos armazenados no Qdrant**  
✅ **Respostas baseadas em documentos** indexados  

## Checklist de Validação

- [ ] Estrutura do projeto criada
- [ ] Processamento de PDFs funcionando
- [ ] Chunking implementado corretamente
- [ ] Documentos indexados no Qdrant
- [ ] API de chat funcionando
- [ ] Histórico de conversa implementado
- [ ] Backend Node integrado com Python
- [ ] Sistema completo testado

## Dicas de Troubleshooting

1. **PDF não processa?** Verifique se PyPDF2 está instalado
2. **Chunks muito grandes?** Ajuste `tamanho_chunk` e `overlap`
3. **Qdrant não conecta?** Verifique se está rodando
4. **Chat sem contexto?** Verifique se documentos foram indexados

## Extras (Opcional)

- Adicionar interface web (React/HTML)
- Suportar múltiplos formatos (TXT, DOCX)
- Implementar busca por documento específico
- Adicionar métricas de qualidade

## Próximos Passos

Após completar este desafio, você estará pronto para:
- Otimizar chunking strategies
- Implementar metadata filtering
- Adicionar re-ranking
- Avançar para técnicas avançadas de RAG

---

**Tempo estimado:** 4-6 horas  
**Dificuldade:** Intermediária a Avançada  
**Importância:** ⭐⭐⭐⭐⭐ (Projeto completo de portfólio)
