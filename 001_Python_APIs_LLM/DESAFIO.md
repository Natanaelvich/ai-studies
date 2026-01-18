# Desafio: Integrando Python com APIs LLM e Prompt Engineering

## Índice

- [Objetivo](#objetivo)
- [Pré-requisitos](#pré-requisitos)
- [Cenário](#cenário)
- [Tasks](#tasks)
  - [Task 1: Configurar Ambiente Python](#task-1-configurar-ambiente-python)
  - [Task 2: Instalar Dependências](#task-2-instalar-dependências)
  - [Task 3: Criar Cliente OpenAI](#task-3-criar-cliente-openai)
  - [Task 4: Implementar Prompt Básico](#task-4-implementar-prompt-básico)
  - [Task 5: Implementar Few-Shot Learning](#task-5-implementar-few-shot-learning)
  - [Task 6: Implementar Chain-of-Thought](#task-6-implementar-chain-of-thought)
  - [Task 7: Testar Variações de Parâmetros](#task-7-testar-variações-de-parâmetros)
  - [Task 8: Criar Função Reutilizável](#task-8-criar-função-reutilizável)
- [Resultados Esperados Finais](#resultados-esperados-finais)
- [Checklist de Validação](#checklist-de-validação)
- [Dicas de Troubleshooting](#dicas-de-troubleshooting)
- [Extras (Opcional)](#extras-opcional)
- [Próximos Passos](#próximos-passos)

---

## Objetivo

Configurar ambiente Python, integrar com APIs LLM (OpenAI/Anthropic), e implementar diferentes técnicas de prompt engineering para entender como diferentes abordagens afetam as respostas dos modelos.

## Pré-requisitos

- Python 3.8+ instalado
- Conta OpenAI ou Anthropic (para chave de API)
- Editor de código (VS Code recomendado)
- Conhecimento básico de linha de comando
- Terminal/Command Prompt funcionando

## Cenário

Você está criando uma aplicação que precisa gerar conteúdo usando IA. Antes de integrar no backend Node.js, você precisa testar diferentes abordagens de prompts em Python para encontrar a melhor estratégia.

## Tasks

### Task 1: Configurar Ambiente Python

**Descrição:** Verificar instalação do Python e criar estrutura do projeto.

**Requisitos:**
- Python 3.8+ instalado
- Verificar versão do Python
- Criar diretório para o projeto
- Criar arquivo `requirements.txt`

**Ações:**
1. Verificar versão do Python:
   ```bash
   python --version
   # ou
   python3 --version
   ```
2. Criar diretório do projeto (se não existir)
3. Criar arquivo `requirements.txt` vazio

**Resultado Esperado:**
- Python 3.8+ instalado e funcionando
- Diretório do projeto criado
- Arquivo `requirements.txt` criado

---

### Task 2: Instalar Dependências

**Descrição:** Instalar bibliotecas necessárias para trabalhar com APIs LLM.

**Requisitos:**
- Instalar biblioteca OpenAI
- Instalar python-dotenv (para variáveis de ambiente)
- Opcional: Instalar Anthropic (se usar Claude)

**Ações:**
1. Adicionar dependências ao `requirements.txt`:
   ```
   openai>=1.0.0
   python-dotenv>=1.0.0
   anthropic>=0.18.0
   ```
2. Instalar dependências:
   ```bash
   pip install -r requirements.txt
   # ou
   pip3 install -r requirements.txt
   ```
3. Verificar instalação:
   ```bash
   pip list | grep openai
   ```

**Resultado Esperado:**
- Bibliotecas `openai` e `python-dotenv` instaladas
- Comando `pip list` mostra as bibliotecas instaladas

---

### Task 3: Criar Cliente OpenAI

**Descrição:** Configurar cliente OpenAI com autenticação usando variáveis de ambiente.

**Requisitos:**
- Criar arquivo `.env` com chave de API
- Criar arquivo Python que carrega variáveis de ambiente
- Criar cliente OpenAI funcional
- Testar conexão básica com a API

**Ações:**
1. Criar arquivo `.env` (na raiz do projeto):
   ```
   OPENAI_API_KEY=sk-...
   ```
2. Adicionar `.env` ao `.gitignore` (se usando Git):
   ```
   .env
   __pycache__/
   *.pyc
   ```
3. Criar arquivo `test_client.py`:
   ```python
   import os
   from dotenv import load_dotenv
   from openai import OpenAI

   # Carregar variáveis de ambiente
   load_dotenv()

   # Criar cliente
   client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

   # Testar conexão
   try:
       response = client.models.list()
       print("✅ Cliente OpenAI configurado com sucesso!")
       print(f"Modelos disponíveis: {len(response.data)} modelos")
   except Exception as e:
       print(f"❌ Erro ao conectar: {e}")
   ```
4. Executar teste:
   ```bash
   python test_client.py
   ```

**Resultado Esperado:**
- Arquivo `.env` criado com chave de API
- Cliente OpenAI criado sem erros
- Teste de conexão executado com sucesso
- Mensagem "✅ Cliente OpenAI configurado com sucesso!" aparece

---

### Task 4: Implementar Prompt Básico

**Descrição:** Criar função que envia prompt simples para o modelo.

**Requisitos:**
- Criar função `prompt_simples(texto)`
- Enviar prompt para GPT-4 ou GPT-3.5-turbo
- Receber e exibir resposta

**Ações:**
1. Criar arquivo `prompt_basico.py`:
   ```python
   import os
   from dotenv import load_dotenv
   from openai import OpenAI

   load_dotenv()
   client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

   def prompt_simples(texto):
       response = client.chat.completions.create(
           model="gpt-3.5-turbo",
           messages=[
               {"role": "user", "content": texto}
           ],
           max_tokens=500
       )
       return response.choices[0].message.content

   # Testar
   resultado = prompt_simples("Explique o que é machine learning em 2 frases.")
   print(resultado)
   ```
2. Executar:
   ```bash
   python prompt_basico.py
   ```

**Resultado Esperado:**
- Função `prompt_simples` criada e funcionando
- Resposta do modelo exibida no console
- Resposta é relevante e completa

---

### Task 5: Implementar Few-Shot Learning

**Descrição:** Criar função que usa exemplos para guiar o modelo.

**Requisitos:**
- Criar função de classificação de sentimento
- Fornecer 3-4 exemplos de classificação
- Testar com novos textos

**Ações:**
1. Criar arquivo `few_shot.py`:
   ```python
   import os
   from dotenv import load_dotenv
   from openai import OpenAI

   load_dotenv()
   client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

   def classificar_sentimento(texto):
       prompt = f"""Classifique os seguintes textos como 'positivo', 'negativo' ou 'neutro':

   Texto: Adorei este produto!
   Sentimento: positivo

   Texto: Não recomendo, muito caro.
   Sentimento: negativo

   Texto: Funciona bem, mas poderia ser melhor.
   Sentimento: neutro

   Texto: Excelente qualidade e atendimento!
   Sentimento: positivo

   Texto: {texto}
   Sentimento:"""

       response = client.chat.completions.create(
           model="gpt-3.5-turbo",
           messages=[
               {"role": "user", "content": prompt}
           ],
           max_tokens=10,
           temperature=0.3  # Baixa temperatura para resultados mais consistentes
       )
       return response.choices[0].message.content.strip()

   # Testar
   textos_teste = [
       "Ótimo serviço, recomendo!",
       "Péssima experiência, não voltarei.",
       "O produto chegou no prazo."
   ]

   for texto in textos_teste:
       sentimento = classificar_sentimento(texto)
       print(f"Texto: {texto}")
       print(f"Sentimento: {sentimento}\n")
   ```
2. Executar e analisar resultados

**Resultado Esperado:**
- Função `classificar_sentimento` criada
- Modelo segue o padrão dos exemplos fornecidos
- Classificações são consistentes (positivo/negativo/neutro)
- Respostas são curtas (apenas a classificação)

---

### Task 6: Implementar Chain-of-Thought

**Descrição:** Criar função que pede ao modelo explicar seu raciocínio passo a passo.

**Requisitos:**
- Criar função que analisa texto complexo
- Solicitar explicação passo a passo
- Receber resposta estruturada

**Ações:**
1. Criar arquivo `chain_of_thought.py`:
   ```python
   import os
   from dotenv import load_dotenv
   from openai import OpenAI

   load_dotenv()
   client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

   def analisar_texto_cot(texto):
       prompt = f"""Analise o seguinte texto e identifique o sentimento. Explique seu raciocínio passo a passo:

   Texto: {texto}

   Análise passo a passo:
   1. Identifique aspectos mencionados:
   2. Avalie o sentimento de cada aspecto:
   3. Pondere os aspectos negativos vs positivos:
   4. Determine o sentimento geral:
   5. Conclusão:"""

       response = client.chat.completions.create(
           model="gpt-3.5-turbo",
           messages=[
               {"role": "user", "content": prompt}
           ],
           max_tokens=500,
           temperature=0.5
       )
       return response.choices[0].message.content

   # Testar com texto complexo
   texto_complexo = "O filme foi interessante e bem dirigido, mas a atuação deixou a desejar e o roteiro teve alguns furos."
   
   resultado = analisar_texto_cot(texto_complexo)
   print("Análise Chain-of-Thought:")
   print(resultado)
   ```
2. Executar e analisar resposta estruturada

**Resultado Esperado:**
- Função `analisar_texto_cot` criada
- Resposta segue estrutura passo a passo
- Raciocínio é explicado claramente
- Conclusão é baseada na análise dos passos anteriores

---

### Task 7: Testar Variações de Parâmetros

**Descrição:** Testar como diferentes valores de temperatura afetam as respostas.

**Requisitos:**
- Testar temperatura baixa (0.2) - respostas determinísticas
- Testar temperatura média (0.7) - balanceada
- Testar temperatura alta (1.0) - criativa
- Comparar resultados

**Ações:**
1. Criar arquivo `test_temperature.py`:
   ```python
   import os
   from dotenv import load_dotenv
   from openai import OpenAI

   load_dotenv()
   client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

   def gerar_com_temperatura(prompt, temperatura):
       response = client.chat.completions.create(
           model="gpt-3.5-turbo",
           messages=[
               {"role": "user", "content": prompt}
           ],
           max_tokens=200,
           temperature=temperatura
       )
       return response.choices[0].message.content

   prompt_teste = "Crie um nome criativo para uma cafeteria que vende café orgânico."

   temperaturas = [0.2, 0.7, 1.0]
   
   print("Testando diferentes temperaturas:\n")
   for temp in temperaturas:
       resultado = gerar_com_temperatura(prompt_teste, temp)
       print(f"Temperatura {temp}:")
       print(f"{resultado}\n")
       print("-" * 50)
   ```
2. Executar e comparar resultados

**Resultado Esperado:**
- Função testa diferentes temperaturas
- Temperatura 0.2: Respostas mais previsíveis e consistentes
- Temperatura 0.7: Respostas balanceadas entre criatividade e consistência
- Temperatura 1.0: Respostas mais variadas e criativas
- Diferenças claras entre as respostas são visíveis

---

### Task 8: Criar Função Reutilizável

**Descrição:** Criar função genérica que pode ser usada em diferentes contextos.

**Requisitos:**
- Função que aceita sistema prompt, mensagem do usuário, e parâmetros
- Tratamento de erros básico
- Retorno estruturado

**Ações:**
1. Criar arquivo `llm_helper.py`:
   ```python
   import os
   from dotenv import load_dotenv
   from openai import OpenAI

   load_dotenv()

   def chamar_llm(
       mensagem_usuario,
       system_prompt=None,
       model="gpt-3.5-turbo",
       temperatura=0.7,
       max_tokens=500
   ):
       """
       Função genérica para chamar LLM.
       
       Args:
           mensagem_usuario: Texto da mensagem do usuário
           system_prompt: Prompt do sistema (opcional)
           model: Modelo a usar (default: gpt-3.5-turbo)
           temperatura: Temperatura do modelo (default: 0.7)
           max_tokens: Máximo de tokens (default: 500)
       
       Returns:
           dict: {"success": bool, "content": str, "error": str}
       """
       try:
           client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))
           
           messages = []
           if system_prompt:
               messages.append({"role": "system", "content": system_prompt})
           messages.append({"role": "user", "content": mensagem_usuario})
           
           response = client.chat.completions.create(
               model=model,
               messages=messages,
               temperature=temperatura,
               max_tokens=max_tokens
           )
           
           return {
               "success": True,
               "content": response.choices[0].message.content,
               "error": None
           }
       except Exception as e:
           return {
               "success": False,
               "content": None,
               "error": str(e)
           }

   # Exemplo de uso
   if __name__ == "__main__":
       # Exemplo 1: Uso simples
       resultado = chamar_llm("Explique o que é RAG.")
       print(resultado["content"])
       
       # Exemplo 2: Com system prompt
       resultado = chamar_llm(
           "O que é Python?",
           system_prompt="Você é um tutor de programação especializado em Python.",
           temperatura=0.5
       )
       print(resultado["content"])
   ```
2. Testar função com diferentes cenários

**Resultado Esperado:**
- Função `chamar_llm` criada e funcionando
- Aceita diferentes parâmetros
- Retorna estrutura consistente
- Tratamento de erros funciona
- Pode ser reutilizada em diferentes contextos

---

## Resultados Esperados Finais

Ao final do desafio, você deve ter:

✅ **Ambiente Python configurado** e funcionando  
✅ **Cliente OpenAI integrado** com autenticação via variáveis de ambiente  
✅ **3 tipos de prompts implementados** (básico, few-shot, chain-of-thought)  
✅ **Testes de temperatura** mostrando diferenças nas respostas  
✅ **Função reutilizável** para chamadas LLM  
✅ **Entendimento prático** de como diferentes abordagens afetam resultados  

## Checklist de Validação

- [ ] Python 3.8+ instalado e funcionando
- [ ] Bibliotecas `openai` e `python-dotenv` instaladas
- [ ] Arquivo `.env` criado com chave de API (não commitado)
- [ ] Cliente OpenAI conecta com sucesso
- [ ] Prompt básico funciona e retorna respostas
- [ ] Few-shot learning segue padrão dos exemplos
- [ ] Chain-of-thought gera análise passo a passo
- [ ] Testes de temperatura mostram diferenças claras
- [ ] Função reutilizável funciona com diferentes parâmetros
- [ ] Tratamento de erros implementado

## Dicas de Troubleshooting

1. **Erro "ModuleNotFoundError"?**
   - Verifique se instalou as dependências: `pip install -r requirements.txt`
   - Verifique se está usando o ambiente Python correto

2. **Erro de autenticação da API?**
   - Verifique se a chave está correta no arquivo `.env`
   - Verifique se está carregando o `.env` com `load_dotenv()`
   - Verifique se o `.env` está na mesma pasta do script

3. **Rate limit errors?**
   - Aguarde alguns segundos entre chamadas
   - Reduza a frequência de testes
   - Verifique limites da sua conta OpenAI

4. **Respostas inconsistentes?**
   - Reduza a temperatura para tarefas determinísticas
   - Use few-shot learning para guiar o modelo
   - Seja mais específico nas instruções

5. **Testando diferentes modelos:**
   ```python
   # Testar GPT-3.5 (mais barato)
   model="gpt-3.5-turbo"
   
   # Testar GPT-4 (mais poderoso, mais caro)
   model="gpt-4"
   ```

## Extras (Opcional)

Se quiser ir além:

- Integrar Anthropic API (Claude) e comparar com OpenAI
- Criar classe Python para encapsular chamadas LLM
- Implementar retry logic para lidar com rate limits
- Criar sistema de cache para respostas
- Medir custos de cada chamada (tokens usados)
- Criar testes unitários para as funções
- Integrar com FastAPI para criar API REST

## Próximos Passos

Após completar este desafio, você estará pronto para:

- Trabalhar com embeddings e vector databases
- Implementar busca por similaridade
- Construir arquitetura RAG completa
- Integrar Python com backend Node.js
- Explorar modelos diferentes e suas capacidades

---

**Tempo estimado:** 2-3 horas  
**Dificuldade:** Iniciante a Intermediária  
**Importância:** ⭐⭐⭐⭐⭐ (Fundamental para todo trabalho com IA)
