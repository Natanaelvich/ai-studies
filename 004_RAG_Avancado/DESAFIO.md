# Desafio: Otimizar RAG com Chunking, Metadata e Re-ranking

## Índice

- [Objetivo](#objetivo)
- [Pré-requisitos](#pré-requisitos)
- [Cenário](#cenário)
- [Tasks](#tasks)
  - [Task 1: Implementar Chunking Hierárquico](#task-1-implementar-chunking-hierárquico)
  - [Task 2: Adicionar Metadata ao Indexar](#task-2-adicionar-metadata-ao-indexar)
  - [Task 3: Implementar Filtros por Metadata](#task-3-implementar-filtros-por-metadata)
  - [Task 4: Implementar Re-ranking](#task-4-implementar-re-ranking)
  - [Task 5: Comparar Performance](#task-5-comparar-performance)
  - [Task 6: Otimizar Pipeline Completo](#task-6-otimizar-pipeline-completo)
- [Resultados Esperados Finais](#resultados-esperados-finais)
- [Checklist de Validação](#checklist-de-validação)
- [Dicas de Troubleshooting](#dicas-de-troubleshooting)
- [Extras (Opcional)](#extras-opcional)
- [Próximos Passos](#próximos-passos)

---

## Objetivo

Otimizar sistema RAG implementando chunking hierárquico, metadata filtering e re-ranking para melhorar precisão e performance.

## Pré-requisitos

- Sistema RAG básico funcionando (módulo 003)
- Python 3.8+ instalado
- Qdrant rodando
- Completou módulos anteriores

## Cenário

Você tem um sistema RAG funcionando, mas precisa otimizar para melhorar precisão e performance usando técnicas avançadas.

## Tasks

### Task 1: Implementar Chunking Hierárquico

**Descrição:** Substituir chunking simples por hierárquico (por parágrafos/seções).

**Ações:**
1. Criar função `chunk_hierarquico` que divide por parágrafos
2. Preservar estrutura do documento
3. Testar com documento longo

**Resultado Esperado:**
- Chunking hierárquico funcionando
- Chunks preservam contexto de parágrafos
- Melhor qualidade que chunking simples

---

### Task 2: Adicionar Metadata ao Indexar

**Descrição:** Adicionar metadados úteis (tipo, data, fonte) ao indexar documentos.

**Ações:**
1. Modificar indexador para incluir metadados
2. Adicionar tipo, data, fonte a cada chunk
3. Armazenar metadados no payload do Qdrant

**Resultado Esperado:**
- Documentos indexados com metadados
- Metadados disponíveis no payload
- Filtros podem ser usados na busca

---

### Task 3: Implementar Filtros por Metadata

**Descrição:** Implementar busca com filtros por tipo, data ou fonte.

**Ações:**
1. Criar função de busca com filtros opcionais
2. Filtrar por tipo de documento
3. Filtrar por data de criação
4. Testar filtros combinados

**Resultado Esperado:**
- Busca com filtros funcionando
- Filtros reduzem espaço de busca
- Performance melhorada com filtros

---

### Task 4: Implementar Re-ranking

**Descrição:** Adicionar re-ranking usando Cross-Encoder ou LLM.

**Ações:**
1. Instalar `sentence-transformers` (Cross-Encoder)
2. Implementar re-ranking de top N resultados
3. Reordenar resultados por relevância mais precisa

**Resultado Esperado:**
- Re-ranking implementado
- Resultados finais mais precisos
- Top N resultados melhor ordenados

---

### Task 5: Comparar Performance

**Descrição:** Comparar qualidade antes e depois das otimizações.

**Ações:**
1. Testar queries com sistema básico
2. Testar queries com otimizações
3. Comparar relevância dos resultados

**Resultado Esperado:**
- Métricas de qualidade coletadas
- Melhora na precisão observada
- Performance comparada

---

### Task 6: Otimizar Pipeline Completo

**Descrição:** Integrar todas as otimizações no pipeline RAG completo.

**Ações:**
1. Atualizar pipeline de indexação
2. Atualizar pipeline de query
3. Testar sistema completo otimizado

**Resultado Esperado:**
- Pipeline completo otimizado
- Todas as técnicas integradas
- Sistema funcionando com melhorias

---

## Resultados Esperados Finais

✅ **Chunking hierárquico** implementado e funcionando  
✅ **Metadata filtering** adicionado ao pipeline  
✅ **Re-ranking** melhorando ordenação final  
✅ **Performance comparada** antes/depois  
✅ **Pipeline completo** otimizado e funcional  

## Checklist de Validação

- [ ] Chunking hierárquico implementado
- [ ] Metadados adicionados ao indexar
- [ ] Filtros por metadata funcionando
- [ ] Re-ranking implementado
- [ ] Performance comparada e melhorada
- [ ] Pipeline completo otimizado

## Dicas de Troubleshooting

1. **Chunks muito grandes?** Ajuste tamanho mínimo/máximo
2. **Filtros não funcionam?** Verifique metadados no payload
3. **Re-ranking lento?** Use Cross-Encoder em vez de LLM

## Extras (Opcional)

- Implementar chunking semântico
- Adicionar mais metadados (autor, categoria)
- Testar diferentes modelos de re-ranking
- Criar métricas automatizadas de qualidade

## Próximos Passos

Após completar este desafio, você estará pronto para:
- Otimizar cache e performance
- Medir custos e otimizar
- Avaliar qualidade do sistema RAG
- Avançar para produção no próximo módulo

---

**Tempo estimado:** 3-4 horas  
**Dificuldade:** Intermediária a Avançada  
**Importância:** ⭐⭐⭐⭐ (Otimizações importantes)
