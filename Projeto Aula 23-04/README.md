# Sistema Distribuído de Gestão de Demandas (SDGD)

## Sobre o Projeto

Sistema web para gerenciamento de demandas em larga escala, permitindo que múltiplos usuários criem, acompanhem e processem tarefas em tempo real. Desenvolvido com foco em **Pensamento Computacional** e **Engenharia de Software**.

-------------------------

## Objetivos

- ✅ Aplicar pensamento computacional na organização de sistemas complexos
- ✅ Demonstrar decomposição modular
- ✅ Trabalhar com fluxo de estados e processamento de tarefas
- ✅ Identificar desafios de escalabilidade e concorrência
- ✅ Aplicar conceitos de engenharia de software em larga escala

-------------------------

## Pensamento Computacional Aplicado

###  Decomposição
Sistema dividido em 5 módulos principais:
- **Autenticação**: Controle de acesso
- **Gestão de Usuários**: CRUD de usuários
- **Gestão de Demandas**: Criação e edição de tarefas
- **Processamento de Status**: Máquina de estados
- **Relatórios**: Dashboard e métricas

###  Reconhecimento de Padrões
Inspirado em sistemas como:
- Kanban (Trello, Jira)
- Sistemas de ticket (Zendesk)
- Workflows empresariais

###  Abstração
Fluxo simplificado:

```
Usuário → Cria Demanda → Processa → Atualiza Status → Conclui

```

###  Algoritmos
Implementação de:
- Validação de dados
- Máquina de estados finitos
- Sistema de filas
- Registro de histórico

-------------------------

## Funcionalidades

- Cadastro e autenticação de usuários
- Criação e edição de demandas
- Sistema de status (Aberto → Em Andamento → Aprovado → Concluído)
- Fila de processamento de tarefas
- Histórico completo de alterações
- Dashboard com métricas em tempo real

-------------------------

## Metodologia de Desenvolvimento

- **Framework Ágil**: Scrum
- **Sprints**: Semanais
- **Controle**: Kanban no GitHub Projects
- **Versionamento**: Git Flow
- **Issues**: GitHub Issues

-------------------------

##  Desafios Técnicos

### Escalabilidade
- Múltiplos usuários simultâneos
- Grande volume de tarefas
- **Solução**: Pool de conexões, cache, paginação

### Consistência de Dados
- Conflitos de status
- Integridade referencial
- **Solução**: Transações ACID, locks otimistas

### Concorrência
- Atualizações simultâneas
- Race conditions
- **Solução**: Versionamento de registros, filas

### Complexidade Estrutural
- Crescimento exponencial de dados
- Performance de queries
- **Solução**: Índices, normalização, arquivamento

-------------------------

##  Métricas do Sistema

- Total de demandas criadas
- Tempo médio de conclusão
- Taxa de aprovação
- Usuários ativos
- Demandas por status

-------------------------
