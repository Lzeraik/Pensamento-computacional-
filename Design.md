# 🎨 Design e Arquitetura do Sistema

## 📐 Arquitetura Geral

### Padrão Arquitetural: MVC + API REST

```
┌─────────────┐
│   Cliente   │ (Frontend - HTML/CSS/JS)
└──────┬──────┘
       │ HTTP/WebSocket
       ↓
┌─────────────┐
│     API     │ (Express.js - Controllers)
└──────┬──────┘
       │
       ↓
┌─────────────┐
│   Serviços  │ (Business Logic)
└──────┬──────┘
       │
       ↓
┌─────────────┐
│   Modelos   │ (Data Access Layer)
└──────┬──────┘
       │
       ↓
┌─────────────┐
│  Database   │ (SQLite/PostgreSQL)
└─────────────┘
```

## 🧩 Decomposição Modular

### 1️⃣ Módulo de Autenticação

**Responsabilidades:**
- Registro de novos usuários
- Login com validação de credenciais
- Geração e validação de tokens JWT
- Controle de sessões

**Componentes:**
- `AuthController.js`
- `AuthService.js`
- `authMiddleware.js`

**Fluxo:**
```
POST /api/auth/register → Validar dados → Hash senha → Criar usuário → Retornar token
POST /api/auth/login → Validar credenciais → Gerar JWT → Retornar token
```

---

### 2️⃣ Módulo de Gestão de Usuários

**Responsabilidades:**
- CRUD de usuários
- Perfis e permissões
- Listagem e busca

**Componentes:**
- `UserController.js`
- `UserModel.js`
- `UserService.js`

**Endpoints:**
```
GET    /api/users          # Listar usuários
GET    /api/users/:id      # Buscar usuário
PUT    /api/users/:id      # Atualizar usuário
DELETE /api/users/:id      # Deletar usuário
```

---

### 3️⃣ Módulo de Gestão de Demandas

**Responsabilidades:**
- Criação de demandas
- Edição e exclusão
- Atribuição de responsáveis
- Busca e filtros

**Componentes:**
- `DemandaController.js`
- `DemandaModel.js`
- `DemandaService.js`

**Endpoints:**
```
POST   /api/demandas       # Criar demanda
GET    /api/demandas       # Listar demandas
GET    /api/demandas/:id   # Buscar demanda
PUT    /api/demandas/:id   # Atualizar demanda
DELETE /api/demandas/:id   # Deletar demanda
```

**Estrutura de Dados:**
```javascript
{
  id: UUID,
  titulo: String,
  descricao: Text,
  status: Enum['aberto', 'em_andamento', 'aprovado', 'concluido'],
  prioridade: Enum['baixa', 'media', 'alta', 'urgente'],
  criador_id: UUID,
  responsavel_id: UUID,
  created_at: Timestamp,
  updated_at: Timestamp
}
```

---

### 4️⃣ Módulo de Processamento de Status

**Responsabilidades:**
- Máquina de estados finitos
- Validação de transições
- Registro de histórico
- Notificações de mudança

**Componentes:**
- `StatusController.js`
- `StatusService.js`
- `HistoricoModel.js`

**Máquina de Estados:**
```
Estados: [aberto, em_andamento, aprovado, concluido, rejeitado]

Transições válidas:
aberto → em_andamento
em_andamento → aprovado | rejeitado
aprovado → concluido
```

### 5️⃣ Módulo de Relatórios

**Responsabilidades:**
- Dashboard com métricas
- Gráficos e visualizações
- Exportação de dados
- Análise de performance

**Componentes:**
- `RelatorioController.js`
- `RelatorioService.js`
- `MetricasService.js`

**Métricas Calculadas:**
- Total de demandas por status
- Tempo médio de conclusão
- Taxa de aprovação/rejeição
- Demandas por usuário
- Demandas por período

---

## 🗄️ Modelagem de Dados

### Diagrama ER

```
┌──────────────┐         ┌──────────────┐
│   usuarios   │         │   demandas   │
├──────────────┤         ├──────────────┤
│ id (PK)      │────┐    │ id (PK)      │
│ nome         │    │    │ titulo       │
│ email        │    └───<│ criador_id   │
│ senha_hash   │    ┌───<│ responsavel  │
│ role         │    │    │ status       │
│ created_at   │    │    │ prioridade   │
└──────────────┘    │    │ descricao    │
                    │    │ created_at   │
                    │    │ updated_at   │
                    │    └──────────────┘
                    │           │
                    │           │
                    │    ┌──────┴───────┐
                    │    │   historico  │
                    │    ├──────────────┤
                    │    │ id (PK)      │
                    │    │ demanda_id   │
                    └────│ usuario_id   │
                         │ status_ant   │
                         │ status_novo  │
                         │ comentario   │
                         │ timestamp    │
                         └──────────────┘
```

### Índices para Performance

```sql
-- Busca rápida por status
CREATE INDEX idx_demandas_status ON demandas(status);

-- Busca por responsável
CREATE INDEX idx_demandas_responsavel ON demandas(responsavel_id);

-- Busca por criador
CREATE INDEX idx_demandas_criador ON demandas(criador_id);

-- Histórico por demanda
CREATE INDEX idx_historico_demanda ON historico(demanda_id);

-- Busca por período
CREATE INDEX idx_demandas_created ON demandas(created_at);
```

---

## 🔄 Fluxos de Dados

### Fluxo de Criação de Demanda

```
1. Usuário preenche formulário
   ↓
2. Frontend valida campos obrigatórios
   ↓
3. POST /api/demandas com dados
   ↓
4. Middleware valida JWT
   ↓
5. Controller recebe requisição
   ↓
6. Service valida regras de negócio
   ↓
7. Model insere no banco
   ↓
8. Registra histórico inicial
   ↓
9. WebSocket notifica usuários
   ↓
10. Retorna demanda criada
```

### Fluxo de Atualização de Status

```
1. Usuário clica em "Atualizar Status"
   ↓
2. Frontend envia PUT /api/demandas/:id/status
   ↓
3. Middleware valida permissões
   ↓
4. Service valida transição de estado
   ↓
5. Model atualiza demanda
   ↓
6. Registra no histórico
   ↓
7. WebSocket notifica mudança
   ↓
8. Frontend atualiza UI
```

---

## Segurança

### Autenticação JWT

```javascript
// Estrutura do Token
{
  header: {
    alg: 'HS256',
    typ: 'JWT'
  },
  payload: {
    userId: 'uuid',
    email: 'user@example.com',
    role: 'user',
    iat: 1234567890,
    exp: 1234571490
  },
  signature: 'hash'
}
```

### Middleware de Autorização

```javascript
function authorize(roles = []) {
  return (req, res, next) => {
    const token = req.headers.authorization?.split(' ')[1];
    
    if (!token) {
      return res.status(401).json({ error: 'Token não fornecido' });
    }
    
    try {
      const decoded = jwt.verify(token, SECRET_KEY);
      
      if (roles.length && !roles.includes(decoded.role)) {
        return res.status(403).json({ error: 'Sem permissão' });
      }
      
      req.user = decoded;
      next();
    } catch (error) {
      return res.status(401).json({ error: 'Token inválido' });
    }
  };
}
```

---

## Escalabilidade

### Estratégias Implementadas

1. **Pool de Conexões**
   - Reutilização de conexões com o banco
   - Limite configurável de conexões simultâneas

2. **Paginação**
   - Limite de 50 registros por página
   - Cursor-based pagination para grandes volumes

3. **Cache**
   - Cache de queries frequentes (Redis)
   - TTL configurável por tipo de dado

4. **Filas de Processamento**
   - Tarefas assíncronas em background
   - Bull Queue para processamento

5. **WebSockets Otimizados**
   - Rooms por contexto (demanda, usuário)
   - Broadcast seletivo

---

## Monitoramento

### Métricas Coletadas

- Tempo de resposta por endpoint
- Taxa de erro
- Conexões ativas
- Uso de memória
- Queries mais lentas

### Logs Estruturados

```javascript
{
  timestamp: '2026-04-29T13:31:00Z',
  level: 'info',
  service: 'demanda-service',
  action: 'criar_demanda',
  userId: 'uuid',
  duration: 45,
  success: true
}
```

---

## Design de Interface

### Princípios

- **Simplicidade**: Interface limpa e intuitiva
- **Responsividade**: Mobile-first
- **Acessibilidade**: WCAG 2.1 Level AA
- **Feedback Visual**: Estados claros de loading/sucesso/erro
