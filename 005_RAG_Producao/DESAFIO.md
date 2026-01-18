# Desafio: Otimizar RAG para Produção - Cache, Performance e Avaliação

## Índice

- [Objetivo](#objetivo)
- [Pré-requisitos](#pré-requisitos)
- [Cenário](#cenário)
- [Tasks](#tasks)
  - [Task 1: Implementar Cache de Respostas](#task-1-implementar-cache-de-respostas)
  - [Task 2: Medir Performance](#task-2-medir-performance)
  - [Task 3: Monitorar Custos](#task-3-monitorar-custos)
  - [Task 4: Criar Testes de Avaliação](#task-4-criar-testes-de-avaliação)
  - [Task 5: Otimizar Pipeline](#task-5-otimizar-pipeline)
  - [Task 6: Documentar Métricas](#task-6-documentar-métricas)
- [Resultados Esperados Finais](#resultados-esperados-finais)
- [Checklist de Validação](#checklist-de-validação)
- [Dicas de Troubleshooting](#dicas-de-troubleshooting)
- [Extras (Opcional)](#extras-opcional)
- [Próximos Passos](#próximos-passos)

---

## Objetivo

Otimizar sistema RAG para produção implementando cache, monitoramento de performance, rastreamento de custos e avaliação de qualidade.

## Pré-requisitos

- Sistema RAG funcionando (módulos anteriores)
- Python 3.8+ instalado
- Redis instalado (opcional, para cache persistente)
- Completou módulos anteriores

## Cenário

Você tem um sistema RAG funcionando e precisa otimizá-lo para produção, focando em cache, performance, custos e avaliação.

## Tasks

### Task 1: Implementar Cache de Respostas

**Descrição:** Adicionar cache para evitar chamadas duplicadas à API.

**Ações:**
1. Implementar cache em memória simples
2. Adicionar TTL (time-to-live) ao cache
3. Testar com queries repetidas

**Resultado Esperado:**
- Cache funcionando
- Queries repetidas usam cache
- Latência reduzida para queries em cache

---

### Task 2: Medir Performance

**Descrição:** Implementar métricas de latência e throughput.

**Ações:**
1. Medir tempo de busca no vector DB
2. Medir tempo de geração do LLM
3. Calcular tempo total de resposta
4. Documentar métricas

**Resultado Esperado:**
- Métricas de latência coletadas
- Tempo de cada etapa medido
- Performance documentada

---

### Task 3: Monitorar Custos

**Descrição:** Rastrear uso de tokens e custos por chamada.

**Ações:**
1. Capturar tokens usados (input/output)
2. Calcular custo por chamada
3. Agregar custos diários/mensais
4. Criar relatório de custos

**Resultado Esperado:**
- Tokens rastreados por chamada
- Custos calculados
- Relatório de custos criado

---

### Task 4: Criar Testes de Avaliação

**Descrição:** Criar testes para avaliar qualidade do sistema.

**Ações:**
1. Criar conjunto de testes (query + resposta esperada)
2. Implementar métricas de precisão/recall
3. Executar testes e coletar scores
4. Documentar resultados

**Resultado Esperado:**
- Testes de avaliação criados
- Métricas de qualidade coletadas
- Resultados documentados

---

### Task 5: Otimizar Pipeline

**Descrição:** Otimizar pipeline com base em métricas coletadas.

**Ações:**
1. Identificar gargalos de performance
2. Otimizar busca (filtros, cache)
3. Otimizar geração (reduzir contexto)
4. Medir melhoria

**Resultado Esperado:**
- Pipeline otimizado
- Performance melhorada
- Custos reduzidos

---

### Task 6: Documentar Métricas

**Descrição:** Documentar métricas, custos e performance.

**Ações:**
1. Criar dashboard básico de métricas
2. Documentar custos médios
3. Documentar latência média
4. Criar relatório de avaliação

**Resultado Esperado:**
- Métricas documentadas
- Dashboard básico criado
- Relatório completo disponível

---

## Resultados Esperados Finais

✅ **Cache de respostas** implementado e funcionando  
✅ **Performance medida** e otimizada  
✅ **Custos monitorados** e controlados  
✅ **Testes de avaliação** criados e executados  
✅ **Pipeline otimizado** para produção  
✅ **Métricas documentadas** e disponíveis  

## Checklist de Validação

- [ ] Cache funcionando e reduzindo chamadas
- [ ] Latência medida e aceitável (< 2s)
- [ ] Custos rastreados e controlados
- [ ] Testes de avaliação criados
- [ ] Pipeline otimizado
- [ ] Métricas documentadas

## Dicas de Troubleshooting

1. **Cache não funciona?** Verifique TTL e limpeza
2. **Performance lenta?** Identifique gargalos (vector DB, LLM)
3. **Custos altos?** Use cache e reduza contexto

## Extras (Opcional)

- Implementar Redis cache persistente
- Criar dashboard de métricas visual
- Implementar alertas de custos
- Adicionar mais métricas de qualidade

## Próximos Passos

Após completar este desafio, você estará pronto para:
- Implementar sistema multi-usuário
- Adicionar controle de acesso
- Implementar logs e observabilidade
- Avançar para sistema corporativo no próximo módulo

---

**Tempo estimado:** 3-4 horas  
**Dificuldade:** Intermediária  
**Importância:** ⭐⭐⭐⭐ (Essencial para produção)
