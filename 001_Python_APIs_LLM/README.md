# Python, APIs LLM e Prompt Engineering

## Índice

- [O que é Python para Devs JS?](#o-que-é-python-para-devs-js)
- [Python Essencial](#python-essencial)
  - [1. Sintaxe Básica](#1-sintaxe-básica)
  - [2. Tipos de Dados](#2-tipos-de-dados)
  - [3. Estruturas de Dados](#3-estruturas-de-dados)
  - [4. Funções e Módulos](#4-funções-e-módulos)
- [APIs de LLM](#apis-de-llm)
  - [1. OpenAI API](#1-openai-api)
  - [2. Anthropic API](#2-anthropic-api)
  - [3. Autenticação e Configuração](#3-autenticação-e-configuração)
- [Prompt Engineering](#prompt-engineering)
  - [1. Tipos de Prompts](#1-tipos-de-prompts)
  - [2. Few-Shot Learning](#2-few-shot-learning)
  - [3. Chain-of-Thought](#3-chain-of-thought)
- [Melhores Práticas](#melhores-práticas)
- [Casos de Uso Comuns](#casos-de-uso-comuns)
- [Próximos Passos](#próximos-passos)

---

## O que é Python para Devs JS?

Python é uma linguagem de programação simples e poderosa, essencial para trabalhar com IA. Para desenvolvedores JavaScript/TypeScript, Python oferece uma sintaxe diferente mas intuitiva, com foco em legibilidade e produtividade.

### Por que Python para IA?

- **Bibliotecas maduras**: NumPy, Pandas, scikit-learn, transformers
- **Integração com modelos**: Hugging Face, LangChain
- **APIs LLM**: Melhor suporte nativo para OpenAI, Anthropic
- **Processamento de dados**: Excelente para manipular textos, embeddings

### Principais Diferenças JS vs Python:

- **Indentação**: Python usa espaços/tabs para blocos (não chaves `{}`)
- **Tipos**: Python é dinamicamente tipado (sem `let`, `const`)
- **Strings**: Aspas simples `'` ou duplas `"` são equivalentes
- **Listas**: Equivalente a arrays JS, mas com métodos diferentes
- **Dicionários**: Similar a objetos JS, mas com sintaxe `{}` para criação

---

## Python Essencial

### 1. **Sintaxe Básica**

**Variáveis:**
```python
# Python não usa let/const/var
nome = "João"
idade = 30
ativo = True
```

**Condicionais:**
```python
if idade >= 18:
    print("Maior de idade")
elif idade >= 13:
    print("Adolescente")
else:
    print("Criança")
```

**Loops:**
```python
# For loop
for i in range(5):
    print(i)  # 0, 1, 2, 3, 4

# Iterar sobre lista
frutas = ["maçã", "banana", "laranja"]
for fruta in frutas:
    print(fruta)
```

**📍 Documentação:** [Python Basics](https://docs.python.org/3/tutorial/introduction.html)

### 2. **Tipos de Dados**

**Tipos Básicos:**
```python
# String
texto = "Olá mundo"
texto_multilinha = """Linha 1
Linha 2"""

# Número
inteiro = 42
decimal = 3.14

# Boolean
verdadeiro = True
falso = False

# None (equivalente a null em JS)
valor_nulo = None
```

**Conversão de Tipos:**
```python
# Convert string to int
numero = int("42")  # 42

# Convert int to string
texto = str(42)  # "42"

# Check type
print(type(numero))  # <class 'int'>
```

### 3. **Estruturas de Dados**

**Listas (Arrays):**
```python
# Criar lista
frutas = ["maçã", "banana", "laranja"]

# Acessar elementos
primeira = frutas[0]  # "maçã"
ultima = frutas[-1]   # "laranja"

# Adicionar elemento
frutas.append("uva")  # ["maçã", "banana", "laranja", "uva"]

# List comprehension (similar a map/filter)
numeros = [1, 2, 3, 4, 5]
dobrados = [x * 2 for x in numeros]  # [2, 4, 6, 8, 10]
```

**Dicionários (Objetos):**
```python
# Criar dicionário
pessoa = {
    "nome": "João",
    "idade": 30,
    "ativo": True
}

# Acessar valores
nome = pessoa["nome"]  # "João"
idade = pessoa.get("idade", 0)  # 30 (com default)

# Adicionar/modificar
pessoa["email"] = "joao@email.com"

# Iterar
for chave, valor in pessoa.items():
    print(f"{chave}: {valor}")
```

**Tuplas (Imutáveis):**
```python
# Criar tupla
coordenadas = (10, 20)

# Acessar
x, y = coordenadas  # x=10, y=20
```

### 4. **Funções e Módulos**

**Definir Função:**
```python
def saudacao(nome, idade=None):
    if idade:
        return f"Olá {nome}, você tem {idade} anos"
    return f"Olá {nome}"

# Chamar função
mensagem = saudacao("João", 30)
```

**Importar Módulos:**
```python
# Import completo
import os

# Import específico
from datetime import datetime

# Import com alias
import numpy as np
```

---

## APIs de LLM

### 1. **OpenAI API**

OpenAI fornece acesso a modelos GPT via API REST.

**Instalação:**
```bash
pip install openai
```

**Uso Básico:**
```python
from openai import OpenAI

client = OpenAI(api_key="sua-chave-api")

response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "Você é um assistente útil."},
        {"role": "user", "content": "Explique o que é RAG."}
    ]
)

print(response.choices[0].message.content)
```

**📍 Documentação:** [OpenAI API Reference](https://platform.openai.com/docs/api-reference)

### 2. **Anthropic API**

Anthropic fornece acesso a modelos Claude via API.

**Instalação:**
```bash
pip install anthropic
```

**Uso Básico:**
```python
import anthropic

client = anthropic.Anthropic(api_key="sua-chave-api")

message = client.messages.create(
    model="claude-3-opus-20240229",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Explique o que é RAG."}
    ]
)

print(message.content[0].text)
```

**📍 Documentação:** [Anthropic API Docs](https://docs.anthropic.com/claude/reference/messages_post)

### 3. **Autenticação e Configuração**

**Variáveis de Ambiente:**
```python
import os
from openai import OpenAI

# Ler da variável de ambiente
api_key = os.getenv("OPENAI_API_KEY")
client = OpenAI(api_key=api_key)
```

**Arquivo `.env`:**
```bash
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
```

**Usando python-dotenv:**
```python
from dotenv import load_dotenv
load_dotenv()  # Carrega variáveis do arquivo .env
```

---

## Prompt Engineering

### 1. **Tipos de Prompts**

**Prompt Direto:**
```python
prompt = "Explique o que é machine learning."
```

**Prompt com Contexto:**
```python
prompt = """
Contexto: Você é um tutor de programação.

Tarefa: Explique o conceito de machine learning para um iniciante.
"""
```

**Prompt Estruturado:**
```python
prompt = """
Analise o seguinte código Python e explique cada linha:

Código:
def soma(a, b):
    return a + b

Instruções:
1. Identifique a função
2. Explique os parâmetros
3. Explique o retorno
"""
```

### 2. **Few-Shot Learning**

Few-shot learning fornece exemplos para guiar o modelo.

**Exemplo:**
```python
prompt = """
Classifique os seguintes textos como 'positivo' ou 'negativo':

Texto: Adorei este produto!
Sentimento: positivo

Texto: Não recomendo, muito caro.
Sentimento: negativo

Texto: Funciona bem, mas poderia ser melhor.
Sentimento: negativo

Texto: Excelente qualidade!
Sentimento:
"""
```

### 3. **Chain-of-Thought**

Chain-of-Thought (CoT) pede ao modelo explicar seu raciocínio passo a passo.

**Exemplo:**
```python
prompt = """
Resolva o seguinte problema passo a passo:

Problema: Se João tem 5 maçãs e come 2, quantas sobraram?

Solução passo a passo:
1. João começou com: 5 maçãs
2. Comeu: 2 maçãs
3. Cálculo: 5 - 2 = 3
4. Resposta: Sobraram 3 maçãs
"""
```

**CoT para Tarefas Complexas:**
```python
prompt = """
Analise este texto e identifique sentimentos. Explique seu raciocínio:

Texto: "O filme foi interessante, mas a atuação deixou a desejar."

Passo 1: Identificar aspectos mencionados
Passo 2: Avaliar sentimento de cada aspecto
Passo 3: Determinar sentimento geral
Passo 4: Conclusão
"""
```

---

## Melhores Práticas

1. **Use system prompts** para definir comportamento consistente do modelo
2. **Seja específico** nas instruções - evite ambiguidade
3. **Forneça exemplos** quando o modelo precisa seguir um formato específico
4. **Teste diferentes temperaturas** - baixa (0-0.3) para tarefas determinísticas, alta (0.7-1) para criatividade
5. **Valide inputs** antes de enviar para a API
6. **Trate erros** da API (rate limits, timeouts)
7. **Use variáveis de ambiente** para chaves de API (nunca hardcode)
8. **Monitore custos** - APIs LLM são pagas por token

## Casos de Uso Comuns

- **Chatbots**: Conversas interativas com contexto
- **Geração de Conteúdo**: Artigos, emails, documentação
- **Análise de Texto**: Sentimento, classificação, extração
- **Tradução**: Tradução entre idiomas
- **Resumo**: Condensar textos longos
- **Código**: Geração e análise de código

## Próximos Passos

Após entender os conceitos básicos, pratique:

- Integrando OpenAI/Anthropic em projetos Node
- Criando diferentes tipos de prompts
- Testando variações de temperatura e parâmetros
- Implementando tratamento de erros
- Explorando modelos diferentes (GPT-4, Claude, etc.)

---

**Recursos Oficiais:**

- [Python Documentation](https://docs.python.org/3/)
- [OpenAI Platform](https://platform.openai.com/)
- [Anthropic Documentation](https://docs.anthropic.com/)
- [Prompt Engineering Guide](https://platform.openai.com/docs/guides/prompt-engineering)
