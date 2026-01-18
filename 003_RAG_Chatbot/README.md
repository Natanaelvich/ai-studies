# RAG Chatbot - Arquitetura Completa

## Índice

- [O que é RAG?](#o-que-é-rag)
- [Arquitetura RAG](#arquitetura-rag)
  - [1. Componentes Principais](#1-componentes-principais)
  - [2. Fluxo de Dados](#2-fluxo-de-dados)
  - [3. Retriever + Generator](#3-retriever--generator)
- [Implementação Prática](#implementação-prática)
  - [1. Upload de Documentos](#1-upload-de-documentos)
  - [2. Processamento e Chunking](#2-processamento-e-chunking)
  - [3. Embeddings e Vector DB](#3-embeddings-e-vector-db)
  - [4. Chat com Histórico](#4-chat-com-histórico)
- [Backend Node + Python](#backend-node--python)
- [Melhores Práticas](#melhores-práticas)
- [Casos de Uso Comuns](#casos-de-uso-comuns)
- [Próximos Passos](#próximos-passos)

---

## O que é RAG?

RAG (Retrieval-Augmented Generation) é uma técnica que combina busca de informações relevantes (retrieval) com geração de texto usando LLMs (generation). Em vez de o modelo gerar respostas apenas com seu conhecimento pré-treinado, RAG busca informações relevantes em documentos e usa como contexto.

### Características Principais:

- **Retrieval**: Busca documentos relevantes usando embeddings
- **Augmentation**: Enriquece prompt com contexto dos documentos
- **Generation**: Gera resposta usando LLM com contexto
- **Contextualizado**: Respostas baseadas em documentos específicos
- **Atualizável**: Adiciona novos documentos sem re-treinar modelo

**Exemplo Visual:**
```
Query: "Como funciona machine learning?"
  ↓
[Retriever] Busca documentos relevantes
  ↓
[Contexto] "Machine learning usa algoritmos..."
  ↓
[LLM] Gera resposta baseada no contexto
  ↓
Resposta: "Machine learning é uma área..."
```

---

## Arquitetura RAG

### 1. **Componentes Principais**

**Pipeline de Indexação:**
- Upload de documentos (PDFs, textos, etc.)
- Processamento e chunking (quebrar em pedaços)
- Criação de embeddings
- Armazenamento em Vector DB

**Pipeline de Query:**
- Query do usuário
- Criação de embedding da query
- Busca de documentos similares (Retriever)
- Contexto para LLM
- Geração de resposta (Generator)

### 2. **Fluxo de Dados**

**Indexação (Upload):**
```
Documento → Chunking → Embeddings → Vector DB
```

**Query (Chat):**
```
Query → Embedding → Search Vector DB → Contexto → LLM → Resposta
```

**Fluxo Completo:**
```
1. Upload de PDF
2. Extração de texto
3. Chunking (quebrar em pedaços)
4. Criar embeddings dos chunks
5. Armazenar embeddings no Qdrant
6. Usuário faz pergunta
7. Criar embedding da pergunta
8. Buscar chunks similares no Qdrant
9. Usar chunks como contexto
10. Chamar LLM com contexto
11. Retornar resposta ao usuário
```

### 3. **Retriever + Generator**

**Retriever:**
- Responsável por buscar documentos relevantes
- Usa embeddings para similarity search
- Retorna top N documentos mais similares
- Pode usar filtros de metadados

**Generator:**
- Responsável por gerar resposta final
- Usa contexto dos documentos recuperados
- LLM (GPT-4, Claude) gera texto
- Combina contexto + pergunta + conhecimento pré-treinado

---

## Implementação Prática

### 1. **Upload de Documentos**

**Suporte a PDFs:**
```python
import PyPDF2

def extrair_texto_pdf(caminho_pdf):
    with open(caminho_pdf, 'rb') as arquivo:
        leitor = PyPDF2.PdfReader(arquivo)
        texto = ""
        for pagina in leitor.pages:
            texto += pagina.extract_text()
    return texto
```

**Upload via API:**
```javascript
// Backend Node.js
app.post('/upload', upload.single('pdf'), async (req, res) => {
  // Salvar PDF
  // Chamar serviço Python para processar
  // Retornar status
});
```

### 2. **Processamento e Chunking**

**Chunking Básico:**
```python
def chunk_texto(texto, tamanho_chunk=1000, overlap=200):
    chunks = []
    inicio = 0
    
    while inicio < len(texto):
        fim = inicio + tamanho_chunk
        chunk = texto[inicio:fim]
        chunks.append(chunk)
        inicio = fim - overlap  # Overlap para contexto
    
    return chunks
```

**Chunking Inteligente:**
```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    length_function=len,
)

chunks = splitter.split_text(texto)
```

### 3. **Embeddings e Vector DB**

**Processo de Indexação:**
```python
# 1. Criar embeddings dos chunks
embeddings = [criar_embedding(chunk) for chunk in chunks]

# 2. Armazenar no Qdrant
points = []
for i, (chunk, embedding) in enumerate(zip(chunks, embeddings)):
    points.append(
        PointStruct(
            id=i,
            vector=embedding,
            payload={"texto": chunk, "documento_id": doc_id}
        )
    )

client.upsert(collection_name="documentos", points=points)
```

### 4. **Chat com Histórico**

**Manter Contexto:**
```python
historico = []

def chat_com_historico(query):
    # 1. Buscar documentos relevantes
    resultados = buscar_similares(query)
    contexto = "\n".join([r.payload["texto"] for r in resultados])
    
    # 2. Montar mensagens com histórico
    mensagens = []
    
    # System prompt
    mensagens.append({
        "role": "system",
        "content": "Você é um assistente que responde perguntas baseado no contexto."
    })
    
    # Histórico de conversa
    for msg in historico[-4:]:  # Últimas 4 mensagens
        mensagens.append(msg)
    
    # Contexto dos documentos
    mensagens.append({
        "role": "user",
        "content": f"Contexto:\n{contexto}\n\nPergunta: {query}"
    })
    
    # 3. Chamar LLM
    resposta = chamar_llm(mensagens)
    
    # 4. Atualizar histórico
    historico.append({"role": "user", "content": query})
    historico.append({"role": "assistant", "content": resposta})
    
    return resposta
```

---

## Backend Node + Python

**Arquitetura:**
```
Frontend (React/HTML)
    ↓
Backend Node.js (Express)
    ↓
Serviço Python (Flask/FastAPI)
    ↓
Qdrant + OpenAI
```

**API Node.js:**
```javascript
// Endpoints principais
POST /upload       // Upload de PDF
POST /chat         // Chat com documentos
GET  /history      // Histórico de chat
```

**Serviço Python:**
```python
# Endpoints principais
POST /process-pdf      // Processar PDF e indexar
POST /query            // Buscar documentos similares
POST /generate         // Gerar resposta com contexto
```

---

## Melhores Práticas

1. **Chunking**: Tamanho de 500-1500 tokens, overlap de 10-20%
2. **Metadados**: Armazene informação útil para filtragem (documento_id, página, etc.)
3. **Histórico**: Limite histórico a 4-6 mensagens mais recentes
4. **Contexto**: Use top 3-5 documentos mais relevantes
5. **Tokens**: Monitore uso de tokens para controle de custos
6. **Validação**: Valide inputs antes de processar
7. **Erros**: Trate erros graciosamente (PDF inválido, API errors)
8. **Performance**: Use batch operations quando possível

## Casos de Uso Comuns

- **Chatbot de documentação**: Responder perguntas sobre documentos técnicos
- **Assistente de conhecimento**: FAQ baseado em documentos
- **Pesquisa acadêmica**: Buscar e resumir papers
- **Suporte técnico**: Respostas baseadas em manuais
- **Análise de documentos**: Extrair informações de contratos, relatórios

## Próximos Passos

Após entender arquitetura RAG completa, você estará pronto para:

- Otimizar chunking strategies para diferentes tipos de documento
- Implementar metadata filtering para busca mais precisa
- Adicionar re-ranking para melhorar resultados
- Implementar cache para otimizar performance
- Avaliar qualidade do sistema RAG
- Avançar para técnicas avançadas de RAG no próximo módulo

---

**Recursos Oficiais:**

- [LangChain RAG Documentation](https://python.langchain.com/docs/use_cases/question_answering/)
- [Qdrant RAG Tutorial](https://qdrant.tech/articles/what-is-rag/)
- [OpenAI RAG Guide](https://platform.openai.com/docs/guides/prompt-engineering/strategy-use-external-tools)
