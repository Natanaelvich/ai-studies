# Desafio: Sistema RAG Corporativo - Multi-usuário, Acesso e Observabilidade

## Índice

- [Objetivo](#objetivo)
- [Pré-requisitos](#pré-requisitos)
- [Cenário](#cenário)
- [Tasks](#tasks)
  - [Task 1: Implementar Autenticação](#task-1-implementar-autenticação)
  - [Task 2: Isolar Dados por Usuário](#task-2-isolar-dados-por-usuário)
  - [Task 3: Implementar Controle de Acesso](#task-3-implementar-controle-de-acesso)
  - [Task 4: Adicionar Logs de Atividade](#task-4-adicionar-logs-de-atividade)
  - [Task 5: Implementar Métricas](#task-5-implementar-métricas)
  - [Task 6: Testar Sistema Completo](#task-6-testar-sistema-completo)
- [Resultados Esperados Finais](#resultados-esperados-finais)
- [Checklist de Validação](#checklist-de-validação)
- [Dicas de Troubleshooting](#dicas-de-troubleshooting)
- [Extras (Opcional)](#extras-opcional)
- [Próximos Passos](#próximos-passos)

---

## Objetivo

Transformar sistema RAG básico em sistema corporativo com suporte a multi-usuário, controle de acesso, logs e observabilidade.

## Pré-requisitos

- Sistema RAG funcionando (módulos anteriores)
- Python 3.8+ instalado
- Conhecimento básico de autenticação
- Completou módulos anteriores

## Cenário

Você tem um sistema RAG funcionando e precisa transformá-lo em sistema corporativo que suporta múltiplos usuários com controle de acesso e observabilidade.

## Tasks

### Task 1: Implementar Autenticação

**Descrição:** Adicionar autenticação básica usando tokens ou sessões.

**Ações:**
1. Criar sistema de autenticação simples (tokens ou sessões)
2. Validar usuário em cada requisição
3. Extrair usuario_id de token/sessão

**Resultado Esperado:**
- Autenticação funcionando
- Usuários autenticados antes de acesso
- usuario_id disponível em requisições

---

### Task 2: Isolar Dados por Usuário

**Descrição:** Filtrar documentos por usuario_id em todas as buscas.

**Ações:**
1. Adicionar usuario_id ao indexar documentos
2. Filtrar por usuario_id em todas as buscas
3. Testar isolamento entre usuários

**Resultado Esperado:**
- Documentos isolados por usuário
- Usuário A não vê documentos de Usuário B
- Isolamento funcionando corretamente

---

### Task 3: Implementar Controle de Acesso

**Descrição:** Adicionar sistema de permissões para documentos.

**Ações:**
1. Criar estrutura de permissões (usuário → documentos)
2. Verificar permissões antes de buscar documentos
3. Implementar permissões por grupo (opcional)

**Resultado Esperado:**
- Permissões funcionando
- Usuários só acessam documentos permitidos
- Controle de acesso implementado

---

### Task 4: Adicionar Logs de Atividade

**Descrição:** Registrar todas as atividades dos usuários.

**Ações:**
1. Criar sistema de logging
2. Logar uploads, queries, deletes
3. Armazenar logs em arquivo ou banco

**Resultado Esperado:**
- Logs de atividade funcionando
- Todas as ações registradas
- Logs disponíveis para auditoria

---

### Task 5: Implementar Métricas

**Descrição:** Coletar métricas de uso do sistema.

**Ações:**
1. Contar queries por usuário
2. Contar documentos por usuário
3. Calcular estatísticas de uso
4. Criar relatório básico

**Resultado Esperado:**
- Métricas coletadas
- Estatísticas de uso disponíveis
- Relatório básico criado

---

### Task 6: Testar Sistema Completo

**Descrição:** Testar sistema completo com múltiplos usuários.

**Ações:**
1. Criar múltiplos usuários de teste
2. Upload documentos por usuário
3. Testar isolamento de dados
4. Testar controle de acesso
5. Verificar logs e métricas

**Resultado Esperado:**
- Sistema completo funcionando
- Múltiplos usuários isolados
- Controle de acesso funcionando
- Logs e métricas coletadas

---

## Resultados Esperados Finais

✅ **Autenticação** implementada e funcionando  
✅ **Isolamento de dados** por usuário  
✅ **Controle de acesso** a documentos  
✅ **Logs de atividade** registrados  
✅ **Métricas** coletadas e disponíveis  
✅ **Sistema corporativo** completo e funcional  

## Checklist de Validação

- [ ] Autenticação funcionando
- [ ] Dados isolados por usuário
- [ ] Controle de acesso implementado
- [ ] Logs de atividade registrados
- [ ] Métricas coletadas
- [ ] Sistema completo testado

## Dicas de Troubleshooting

1. **Autenticação falha?** Verifique tokens/sessões
2. **Usuário vê documentos de outros?** Verifique filtros por usuario_id
3. **Permissões não funcionam?** Verifique estrutura de permissões

## Extras (Opcional)

- Implementar JWT tokens
- Adicionar permissões por grupo
- Criar dashboard de métricas
- Implementar rate limiting

## Próximos Passos

Após completar este desafio, você terá concluído as FASES 1 e 2 do roadmap:

- ✅ Base sólida de IA aplicada (FASE 1)
- ✅ RAG profissional & automações (FASE 2)

Próximas etapas (FASE 3 e 4):
- Agents & automações
- Fine-tuning e avaliação avançada
- Produto SaaS completo

---

**Tempo estimado:** 4-6 horas  
**Dificuldade:** Intermediária a Avançada  
**Importância:** ⭐⭐⭐⭐⭐ (Sistema completo de portfólio)
