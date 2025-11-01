# Анализ репозитория: ElizaOS

## 📋 Общий обзор

**ElizaOS** — это комплексная open-source платформа для разработки мультиагентных AI-приложений, построенная как TypeScript монорепозиторий с использованием Bun в качестве основного runtime и package manager'а. Платформа предоставляет модульную архитектуру с плагинной системой для создания, развертывания и управления автономными AI-агентами.

### Тип проекта
- **Архитектура**: TypeScript Monorepo
- **Управление**: Turbo (build orchestration) + Lerna (versioning/publishing)
- **Runtime**: Bun 1.2.21 + Node.js 23.3.0
- **Версия**: 1.6.4-alpha.18

### Основное назначение
ElizaOS позволяет разработчикам создавать интеллектуальных агентов с расширяемыми возможностями через систему плагинов. Платформа поддерживает множество интеграций (Discord, Telegram, Twitter), model-agnostic AI коннекторы (OpenAI, Anthropic, Google, Grok), и предоставляет профессиональный веб-интерфейс для управления агентами.

---

## 📁 Структура проекта

```
eliza/
├── packages/                    # Монорепозиторий пакетов
│   ├── core/                   # Ядро фреймворка (@elizaos/core)
│   │   ├── src/
│   │   │   ├── runtime.ts      # AgentRuntime - центральный оркестратор
│   │   │   ├── database.ts     # DatabaseAdapter интерфейс
│   │   │   ├── types/          # Все типы TypeScript
│   │   │   ├── services/       # Базовые сервисы
│   │   │   ├── utils/          # Утилиты (UUID, buffer, search)
│   │   │   └── __tests__/      # Unit тесты с Bun
│   │   └── build.ts            # Кастомная сборка (node/browser targets)
│   │
│   ├── server/                 # Express + Socket.IO сервер (@elizaos/server)
│   │   ├── src/
│   │   │   ├── index.ts        # Главный файл сервера (1859 строк)
│   │   │   ├── api/            # REST API эндпоинты
│   │   │   ├── middleware/     # Auth, rate-limiting, validation
│   │   │   ├── socketio/       # WebSocket обработчики
│   │   │   ├── services/       # Message bus, internal services
│   │   │   └── loader.ts       # Загрузка character definitions
│   │   └── __tests__/          # Интеграционные тесты
│   │
│   ├── client/                 # React 19 Web UI (@elizaos/client)
│   │   ├── src/
│   │   │   ├── App.tsx         # Главный компонент
│   │   │   ├── components/     # React компоненты (UI, sidebar, chat)
│   │   │   ├── routes/         # React Router 7 маршруты
│   │   │   ├── hooks/          # Custom hooks + TanStack Query
│   │   │   ├── context/        # Context providers (Auth, Connection)
│   │   │   └── lib/            # API client config, logger
│   │   ├── cypress/            # E2E тесты с Cypress
│   │   └── vite.config.ts      # Vite конфигурация
│   │
│   ├── cli/                    # Command-line interface (@elizaos/cli)
│   │   ├── src/
│   │   │   ├── index.ts        # CLI entry point (Commander.js)
│   │   │   ├── commands/       # CLI команды (create, test, dev, start)
│   │   │   ├── utils/          # bun-exec утилиты
│   │   │   └── templates/      # Шаблоны проектов
│   │   └── tests/              # BATS + unit тесты
│   │
│   ├── api-client/             # Type-safe API client (@elizaos/api-client)
│   │   └── src/                # Типизированные сервисы для REST API
│   │
│   ├── plugin-sql/             # Postgres/PGLite adapter (@elizaos/plugin-sql)
│   │   ├── src/
│   │   │   ├── base.ts         # BaseDrizzleAdapter (3667 строк)
│   │   │   ├── pg/             # PostgreSQL адаптер
│   │   │   ├── pglite/         # PGLite адаптер
│   │   │   ├── schema/         # Drizzle ORM схемы
│   │   │   ├── rls.ts          # Row-Level Security
│   │   │   └── migration-service.ts
│   │   └── drizzle/            # SQL миграции
│   │
│   ├── plugin-bootstrap/       # Базовые actions/providers/evaluators
│   │   ├── src/
│   │   │   ├── actions/        # REPLY, SEND_MESSAGE, IGNORE, и др.
│   │   │   ├── providers/      # TIME, FACTS, BOREDOM, и др.
│   │   │   ├── evaluators/     # Goal, fact checkers
│   │   │   └── services/       # TaskService, EmbeddingService
│   │   └── index.ts            # Главный плагин экспорт
│   │
│   ├── plugin-starter/         # Шаблон для создания плагинов
│   ├── project-starter/        # Шаблон standalone проекта
│   ├── project-tee-starter/    # TEE (Trusted Execution Env) шаблон
│   ├── test-utils/             # Утилиты для тестирования
│   ├── service-interfaces/     # Интерфейсы для сервисов плагинов
│   ├── app/                    # Tauri desktop/mobile приложение
│   └── elizaos/                # Alias для CLI package
│
├── scripts/                     # Bash скрипты (Docker, инициализация)
├── examples/                    # Примеры использования
├── docs/                        # Документация проекта
├── .github/workflows/           # CI/CD (GitHub Actions)
│   └── ci.yaml                 # Тесты, линтинг, сборка
│
├── package.json                # Root monorepo конфигурация
├── turbo.json                  # Turbo pipeline конфигурация
├── lerna.json                  # Lerna версионирование
├── tsconfig.json               # TypeScript конфигурация
├── Dockerfile                  # Multi-stage Docker образ
├── docker-compose.yaml         # Postgres + Eliza сервисы
└── .prettierrc                 # Prettier форматирование
```

### Описание директорий

- **packages/core**: Фундамент платформы - AgentRuntime, система плагинов, типы, database adapters, memory/search, логирование
- **packages/server**: HTTP/WebSocket сервер с Express 5, Socket.IO, rate limiting, Sentry, миграциями БД
- **packages/client**: React 19 SPA с Vite, Tailwind CSS 4, TanStack Query, real-time чат, agent management dashboard
- **packages/cli**: Commander-based CLI для scaffolding, управления агентами, dev servers, тестирования
- **packages/api-client**: Типизированный HTTP клиент для взаимодействия с server API
- **packages/plugin-sql**: DatabaseAdapter реализация на Drizzle ORM (PostgreSQL/PGLite + pgvector)
- **packages/plugin-bootstrap**: Дефолтные actions (REPLY, SEND_MESSAGE), providers (TIME, FACTS), evaluators
- **scripts/**: Bash утилиты для Docker, git submodules, development workflows

---

## 🛠 Технологический стек

### Frontend

| Технология | Версия | Назначение |
|------------|--------|------------|
| **React** | 19.1.0 | UI библиотека |
| **React Router** | 7.3.0 | Клиентский роутинг |
| **Vite** | 7.1.7 | Build tool и dev server |
| **TypeScript** | 5.9.2 | Типизация |
| **Tailwind CSS** | 4.1.0 | CSS фреймворк |
| **Radix UI** | Various | Headless UI компоненты |
| **TanStack Query** | 5.67.2 | Управление серверным state |
| **Socket.IO Client** | 4.8.1 | Real-time WebSocket |
| **Lucide React** | 0.544.0 | Иконки |
| **React Force Graph** | 1.47.6 | Визуализация графов |
| **Cypress** | 15.3.0 | E2E тестирование |
| **Shiki** | 3.6.0 | Syntax highlighting |

**Build & Dev Tools:**
- **@vitejs/plugin-react-swc**: Fast Refresh с SWC
- **@tailwindcss/vite**: Tailwind v4 интеграция
- **rollup-plugin-visualizer**: Анализ bundle размера
- **ESLint** 9.22.0 + **Prettier** 3.6.2: Линтинг и форматирование

### Backend

| Технология | Версия | Назначение |
|------------|--------|------------|
| **Node.js** | 23.3.0 | Runtime (для совместимости) |
| **Bun** | 1.2.21 | Основной runtime и package manager |
| **Express** | 5.1.0 | HTTP сервер |
| **Socket.IO** | 4.8.1 | WebSocket real-time коммуникация |
| **TypeScript** | 5.9.2 | Типизация |
| **Drizzle ORM** | 0.44.2 | SQL ORM |
| **Helmet** | 8.1.0 | Security headers |
| **express-rate-limit** | 8.1.0 | Rate limiting |
| **Multer** | 2.0.1 | File upload обработка |
| **@sentry/node** | 10.16.0 | Error tracking |
| **Dotenv** | 17.2.3 | Env переменные |

**CLI Tools:**
- **Commander** 14.0.0: CLI framework
- **Chalk** 5.4.1: Terminal стилизация
- **Ora** 9.0.0: Spinner индикаторы
- **Prompts** (@clack/prompts): Интерактивные CLI prompts

### База данных

| Технология | Версия | Назначение |
|------------|--------|------------|
| **PostgreSQL** | Latest (via ankane/pgvector) | Production БД |
| **PGLite** | 0.3.3 | Встроенная Postgres для dev/testing |
| **pgvector** | Latest | Векторные эмбеддинги для semantic search |
| **Drizzle ORM** | 0.44.2 | Type-safe SQL query builder |
| **Drizzle Kit** | 0.31.1 | Schema migrations |

**Особенности БД:**
- Row-Level Security (RLS) для multi-tenancy
- Vector similarity search (cosine distance)
- Multi-dimensional embeddings (384/768/1024/1536/3072 размерности)
- Миграции через Drizzle Kit

### DevOps & Инфраструктура

| Технология | Назначение |
|------------|------------|
| **Turbo** 2.5.5 | Monorepo build orchestration, caching |
| **Lerna** 9.0.0 | Versioning и publishing пакетов |
| **Docker** | Контейнеризация (multi-stage build) |
| **Docker Compose** | Локальная разработка (Postgres + App) |
| **GitHub Actions** | CI/CD (тесты, линтинг, сборка) |
| **Husky** 9.1.7 | Git hooks |
| **Prettier** 3.6.2 | Code formatting |
| **Bun Test** | Unit/integration testing |
| **Cypress** 15.3.0 | E2E testing (client) |

**CI/CD Pipeline:**
- Concurrency control для экономии ресурсов
- Turbo remote caching
- Тесты с coverage (bun test)
- Автоматический format check
- Build verification

---

## 🏗 Архитектура

### Frontend Architecture

#### Компонентная структура
ElizaOS client построен на **React 19** с использованием функциональных компонентов и hooks. Архитектура следует принципам:

**1. Feature-based организация:**
```
components/
├── agent-creator/      # Создание агентов
├── agent-log-viewer/   # Логи агентов
├── app-sidebar/        # Навигация
├── ui/                 # Переиспользуемые Radix UI компоненты
└── onboarding-tour/    # Joyride tutorial
```

**2. Routing с React Router 7:**
```typescript
// App.tsx - декларативный роутинг
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="chat/:agentId/:channelId" element={<Chat />} />
  <Route path="settings/:agentId" element={<AgentSettingsRoute />} />
  <Route path="agents/new" element={<AgentCreatorRoute />} />
  <Route path="group/:channelId" element={<GroupChannel />} />
</Routes>
```

**3. State Management:**

**TanStack Query** для серверного state:
```typescript
// hooks/use-query-hooks.ts
export const STALE_TIMES = {
  FREQUENT: 5000,      // Часто обновляемые данные
  STANDARD: 30000,     // Стандартные данные
  STATIC: 300000,      // Статичные данные
};

// Prefetch с error handling
await queryClient.prefetchQuery({
  queryKey: ['agents'],
  queryFn: async () => {
    const result = await elizaClient.agents.listAgents();
    return { data: result };
  },
  staleTime: STALE_TIMES.FREQUENT,
});
```

**React Context** для глобального состояния:
```typescript
// context/AuthContext.tsx - Аутентификация
// context/ConnectionContext.tsx - WebSocket статус
<ConnectionProvider>
  <AuthProvider>
    <AppContent />
  </AuthProvider>
</ConnectionProvider>
```

**4. Real-time коммуникация:**
```typescript
// Socket.IO client для live updates
import { io } from 'socket.io-client';

const socket = io('http://localhost:3000', {
  transports: ['websocket', 'polling'],
});

socket.on('message', (data) => {
  // Handle real-time messages
});
```

**5. UI Component Pattern (Radix UI):**
```typescript
// Композиция headless компонентов с Tailwind
import { Dialog, DialogContent, DialogTitle } from '@/components/ui/dialog';

<Dialog open={isOpen} onOpenChange={setIsOpen}>
  <DialogContent className="sm:max-w-[600px]">
    <DialogTitle>Create Agent</DialogTitle>
    {/* Content */}
  </DialogContent>
</Dialog>
```

**6. API Layer:**
```typescript
// lib/api-client-config.ts
import { ElizaClient } from '@elizaos/api-client';

export const createElizaClient = () => {
  return new ElizaClient({
    baseUrl: 'http://localhost:3000',
    apiKey: localStorage.getItem('apiKey'),
  });
};

// Использование в компонентах
const { data: agents } = useQuery({
  queryKey: ['agents'],
  queryFn: () => createElizaClient().agents.listAgents(),
});
```

#### Стилизация и UI/UX

**Tailwind CSS v4** с кастомными стилями:
```css
/* index.css - CSS Variables для темизации */
@layer base {
  :root {
    --background: 224 71.4% 4.1%;
    --foreground: 210 20% 98%;
    --card: 224 71.4% 4.1%;
    --primary: 210 20% 98%;
    /* ... */
  }
}
```

**Адаптивный дизайн:**
- Mobile-first подход
- Breakpoints: `sm:`, `md:`, `lg:`
- Мобильное меню с Sheet component

**Accessibility:**
- `sr-only` классы для screen readers
- ARIA атрибуты в Radix компонентах
- Keyboard navigation support

---

### Backend Architecture

#### Архитектурные слои

**1. AgentRuntime - Центральный оркестратор**

```typescript
// packages/core/src/runtime.ts
export class AgentRuntime implements IAgentRuntime {
  readonly agentId: UUID;
  readonly character: Character;
  public adapter: IDatabaseAdapter;
  
  // Компоненты плагинов
  readonly actions: Action[] = [];
  readonly evaluators: Evaluator[] = [];
  readonly providers: Provider[] = [];
  readonly plugins: Plugin[] = [];
  
  // Сервисы и модели
  services = new Map<ServiceTypeName, Service[]>();
  models = new Map<string, ModelHandler[]>();
  
  // Обработчики
  private eventHandlers: Map<string, ((data: any) => void)[]>;
  private sendHandlers = new Map<string, SendHandlerFunction>();
  private taskWorkers = new Map<string, TaskWorker>();
  
  // State management
  stateCache = new Map<string, State>();
  
  async processMessage(message: Memory, state?: State): Promise<Memory[]> {
    // 1. Валидация действия
    // 2. Выполнение action цепочки
    // 3. Возврат результатов
  }
}
```

**2. Plugin System Architecture**

Каждый плагин следует единой структуре:

```typescript
// packages/core/src/types/plugin.ts
interface Plugin {
  name: string;
  description: string;
  
  // Компоненты
  actions?: Action[];        // User-facing команды
  services?: Service[];      // Stateful интеграции
  providers?: Provider[];    // Контекстная информация (read-only)
  evaluators?: Evaluator[];  // Пост-обработка
  
  // События и роуты
  events?: PluginEvents;
  routes?: Route[];
  
  // Миграции и схемы
  schema?: any;
}
```

**Пример Action:**
```typescript
// packages/plugin-bootstrap/src/actions/reply.ts
export const replyAction = {
  name: 'REPLY',
  similes: ['GREET', 'REPLY_TO_MESSAGE', 'RESPOND'],
  description: 'Replies to conversation with generated message',
  
  validate: async (runtime: IAgentRuntime) => true,
  
  handler: async (
    runtime: IAgentRuntime,
    message: Memory,
    state: State,
    options: any,
    callback: HandlerCallback
  ): Promise<ActionResult> => {
    // 1. Compose state with providers
    state = await runtime.composeState(message, ['RECENT_MESSAGES']);
    
    // 2. Generate response via LLM
    const response = await runtime.useModel(ModelType.TEXT_LARGE, {
      prompt: composePrompt(state),
    });
    
    // 3. Send via callback
    await callback({ text: response });
    
    // 4. Return ActionResult for chaining
    return {
      text: `Generated reply: ${response}`,
      success: true,
      values: { responded: true },
    };
  },
  
  examples: [/* ... */],
} as Action;
```

**3. Service Architecture**

Services - это stateful компоненты для внешних интеграций:

```typescript
// packages/core/src/types/service.ts
abstract class Service {
  abstract name: ServiceTypeName;
  
  async initialize(runtime: IAgentRuntime): Promise<void> {}
  async start(): Promise<void> {}
  async stop(): Promise<void> {}
}

// Использование в runtime
const walletService = runtime.getService<WalletService>('wallet');
const balance = await walletService.getBalance();
```

**4. Provider Pattern**

Providers поставляют read-only контекст:

```typescript
// Provider возвращает форматированный текст для промпта
export const timeProvider = {
  name: 'TIME',
  description: 'Provides current time information',
  
  get: async (runtime, message, state) => {
    const now = new Date();
    return {
      text: `Current time: ${now.toISOString()}`,
      values: { timestamp: now.getTime() },
    };
  },
} as Provider;
```

**5. Database Adapter Pattern**

```typescript
// packages/plugin-sql/src/base.ts
export abstract class BaseDrizzleAdapter extends DatabaseAdapter<any> {
  protected abstract withDatabase<T>(operation: () => Promise<T>): Promise<T>;
  
  // Операции с агентами
  async createAgent(agent: Agent): Promise<void> { /* ... */ }
  async getAgent(agentId: UUID): Promise<Agent | null> { /* ... */ }
  
  // Операции с памятью
  async createMemory(memory: Memory): Promise<void> { /* ... */ }
  async searchMemoriesByEmbedding(
    embedding: number[],
    options: SearchOptions
  ): Promise<Memory[]> { /* ... */ }
  
  // Транзакции
  async withTransaction<T>(fn: () => Promise<T>): Promise<T> { /* ... */ }
}
```

**6. Event System (EventTarget, НЕ EventEmitter)**

```typescript
// ВАЖНО: Используется нативный EventTarget, НЕ Node.js EventEmitter
class MessageService extends EventTarget {
  emit(event: string, data: any) {
    this.dispatchEvent(new CustomEvent(event, { detail: data }));
  }
  
  on(event: string, handler: (data: any) => void) {
    const wrappedHandler = ((e: CustomEvent) => handler(e.detail));
    this.addEventListener(event, wrappedHandler);
  }
}
```

---

### API Design

#### REST API Endpoints

**Server Structure:**
```typescript
// packages/server/src/api/index.ts
export function createApiRouter(agentServer: AgentServer) {
  const router = express.Router();
  
  // Agent management
  router.get('/agents', listAgentsHandler);
  router.post('/agents', createAgentHandler);
  router.get('/agents/:agentId', getAgentHandler);
  router.delete('/agents/:agentId', deleteAgentHandler);
  
  // Messaging
  router.post('/agents/:agentId/message', sendMessageHandler);
  router.get('/agents/:agentId/channels/:channelId/messages', getMessagesHandler);
  
  // Groups
  router.post('/groups', createGroupHandler);
  router.get('/groups', listGroupsHandler);
  
  // System
  router.get('/health', healthCheckHandler);
  router.get('/version', versionHandler);
  
  return router;
}
```

**Authentication:**
```typescript
// API Key based auth
app.use('/api', apiKeyAuthMiddleware);

// В middleware
const apiKey = req.headers?.['x-api-key'];
if (apiKey !== process.env.ELIZA_SERVER_AUTH_TOKEN) {
  return res.status(401).send('Unauthorized');
}
```

**Rate Limiting:**
```typescript
// packages/server/src/middleware/rate-limit.ts
export const createApiRateLimit = () => rateLimit({
  windowMs: 15 * 60 * 1000, // 15 минут
  max: 100,                  // 100 запросов
  message: 'Too many requests',
});

app.use('/api/', createApiRateLimit());
```

#### WebSocket API (Socket.IO)

```typescript
// packages/server/src/socketio/index.ts
export function setupSocketIO(io: SocketIOServer, agentServer: AgentServer) {
  io.on('connection', (socket) => {
    // Join agent room
    socket.on('join:agent', async (agentId: string) => {
      socket.join(`agent:${agentId}`);
    });
    
    // Send message
    socket.on('message:send', async (data) => {
      const result = await agentServer.processMessage(data);
      io.to(`agent:${data.agentId}`).emit('message:new', result);
    });
    
    // Typing indicators
    socket.on('typing:start', (data) => {
      socket.to(`agent:${data.agentId}`).emit('typing', data);
    });
  });
}
```

**Security Headers (Helmet):**
```typescript
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", "'unsafe-inline'"],
    },
  },
  hsts: { maxAge: 31536000 },
}));
```

---

## 💾 Модели данных и БД

### Drizzle ORM Schema

**Core Tables:**

```typescript
// packages/plugin-sql/src/schema/index.ts

// Агенты
export const agentTable = pgTable('agents', {
  id: uuid('id').primaryKey(),
  name: varchar('name', { length: 255 }).notNull(),
  bio: text('bio'),
  settings: jsonb('settings'),
  createdAt: timestamp('created_at').defaultNow(),
});

// Сообщения
export const messageTable = pgTable('messages', {
  id: uuid('id').primaryKey(),
  agentId: uuid('agent_id').references(() => agentTable.id),
  roomId: uuid('room_id').references(() => roomTable.id),
  senderId: uuid('sender_id'),
  content: jsonb('content').notNull(), // Content type
  createdAt: timestamp('created_at').defaultNow(),
});

// Память (с embeddings)
export const memoryTable = pgTable('memories', {
  id: uuid('id').primaryKey(),
  agentId: uuid('agent_id').references(() => agentTable.id),
  roomId: uuid('room_id'),
  content: jsonb('content'),
  embedding: vector('embedding', { dimensions: 384 }), // pgvector
  metadata: jsonb('metadata'),
  createdAt: timestamp('created_at').defaultNow(),
});

// Комнаты (channels)
export const roomTable = pgTable('rooms', {
  id: uuid('id').primaryKey(),
  name: varchar('name', { length: 255 }),
  worldId: uuid('world_id').references(() => worldTable.id),
  channelType: varchar('channel_type', { length: 50 }),
  metadata: jsonb('metadata'),
});

// Relationships
export const relationshipTable = pgTable('relationships', {
  id: uuid('id').primaryKey(),
  entityAId: uuid('entity_a_id').references(() => entityTable.id),
  entityBId: uuid('entity_b_id').references(() => entityTable.id),
  type: varchar('type', { length: 50 }),
  metadata: jsonb('metadata'),
});
```

### Vector Search Implementation

```typescript
// packages/plugin-sql/src/base.ts
async searchMemoriesByEmbedding(
  embedding: number[],
  options: {
    match_threshold?: number;
    count?: number;
    agentId?: UUID;
    roomId?: UUID;
  }
): Promise<Memory[]> {
  const embeddingCol = DIMENSION_MAP[embedding.length];
  
  return await this.withDatabase(async () => {
    const results = await this.db
      .select()
      .from(memoryTable)
      .where(and(
        eq(memoryTable.agentId, options.agentId),
        options.roomId ? eq(memoryTable.roomId, options.roomId) : undefined
      ))
      .orderBy(
        cosineDistance(memoryTable[embeddingCol], embedding)
      )
      .limit(options.count || 10);
    
    return results.map(row => this.deserializeMemory(row));
  });
}
```

### Row-Level Security (RLS)

```typescript
// packages/plugin-sql/src/rls.ts
export async function installRLSFunctions(db: NodePgDatabase) {
  // Create context setter function
  await db.execute(sql`
    CREATE OR REPLACE FUNCTION set_owner_context(owner_id_param UUID)
    RETURNS void AS $$
    BEGIN
      PERFORM set_config('app.current_owner_id', owner_id_param::text, false);
    END;
    $$ LANGUAGE plpgsql;
  `);
  
  // Apply RLS policies
  await db.execute(sql`
    ALTER TABLE agents ENABLE ROW LEVEL SECURITY;
    
    CREATE POLICY agents_owner_policy ON agents
      USING (owner_id = current_setting('app.current_owner_id')::uuid);
  `);
}
```

### Миграции

```typescript
// packages/plugin-sql/src/migration-service.ts
export class DatabaseMigrationService {
  private schemas = new Map<string, any>();
  
  registerSchema(pluginName: string, schema: any) {
    this.schemas.set(pluginName, schema);
  }
  
  async runAllPluginMigrations(options?: {
    verbose?: boolean;
    dryRun?: boolean;
  }): Promise<void> {
    for (const [name, schema] of this.schemas) {
      await this.migratePluginSchema(name, schema, options);
    }
  }
}
```

**Команды миграций:**
```bash
# Генерация миграций
bun run migrate:generate

# Применение миграций
bun run migrate
```

---

## 🎨 UI/UX и стилизация

### Design System

ElizaOS использует **Radix UI** компоненты с кастомной темизацией через **Tailwind CSS v4**.

**Компоненты:**
```typescript
// packages/client/src/components/ui/
├── button.tsx          // Радиксовая кнопка с вариантами
├── dialog.tsx          // Модальные окна
├── dropdown-menu.tsx   // Выпадающие меню
├── toast.tsx           // Уведомления
├── scroll-area.tsx     // Кастомные scrollbars
├── sidebar.tsx         // Навигационная панель
├── sheet.tsx           // Слайд-ауты (для mobile)
└── tooltip.tsx         // Всплывающие подсказки
```

**Theme Configuration:**
```typescript
// tailwind.config.ts
export default {
  theme: {
    extend: {
      colors: {
        background: 'hsl(var(--background))',
        foreground: 'hsl(var(--foreground))',
        primary: 'hsl(var(--primary))',
        secondary: 'hsl(var(--secondary))',
        accent: 'hsl(var(--accent))',
        destructive: 'hsl(var(--destructive))',
      },
      borderRadius: {
        lg: 'var(--radius)',
        md: 'calc(var(--radius) - 2px)',
        sm: 'calc(var(--radius) - 4px)',
      },
    },
  },
};
```

### Component Variants (CVA)

```typescript
// class-variance-authority для типобезопасных вариантов
import { cva } from 'class-variance-authority';

const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-md text-sm font-medium',
  {
    variants: {
      variant: {
        default: 'bg-primary text-primary-foreground hover:bg-primary/90',
        destructive: 'bg-destructive text-destructive-foreground',
        outline: 'border border-input bg-background hover:bg-accent',
        ghost: 'hover:bg-accent hover:text-accent-foreground',
      },
      size: {
        default: 'h-10 px-4 py-2',
        sm: 'h-9 rounded-md px-3',
        lg: 'h-11 rounded-md px-8',
        icon: 'h-10 w-10',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'default',
    },
  }
);
```

### Адаптивность

**Mobile-First подход:**
```tsx
<div className="flex flex-col md:flex-row gap-4">
  {/* Вертикальный на mobile, горизонтальный на desktop */}
</div>

<div className="w-full md:max-w-4xl">
  {/* Полная ширина на mobile, ограничена на desktop */}
</div>

{/* Mobile меню */}
<div className="md:hidden">
  <Sheet>
    <SheetTrigger><Menu /></SheetTrigger>
    <SheetContent><AppSidebar /></SheetContent>
  </Sheet>
</div>
```

### Accessibility

- **Keyboard Navigation**: Все интерактивные элементы доступны через Tab
- **Screen Reader Support**: `sr-only` классы, ARIA атрибуты
- **Focus Management**: Видимые focus states
- **Semantic HTML**: Правильное использование `<button>`, `<nav>`, `<main>`

---

## ✅ Качество кода

### Линтинг и форматирование

**Prettier Configuration:**
```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 100,
  "endOfLine": "lf"
}
```

**TypeScript Strict Mode:**
```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "target": "ES2022",
    "module": "ES2022",
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

**Соглашения по именованию:**
- Variables/Functions: `camelCase`
- React Components: `PascalCase`
- Types/Interfaces: `PascalCase`
- Constants: `UPPER_SNAKE_CASE`
- Files: Match main export (`DashboardMenu.tsx`, `useAgents.ts`)

### Тестирование

**Unit Tests (Bun Test):**
```typescript
// packages/core/src/__tests__/character.test.ts
import { describe, test, expect } from 'bun:test';

describe('Character Config Functions', () => {
  test('should parse character object', () => {
    const character = { name: 'TestChar', bio: ['Test'] };
    const parsed = parseCharacter(character);
    expect(parsed.name).toBe('TestChar');
  });
  
  test('should validate character', () => {
    const result = validateCharacterConfig(character);
    expect(result.isValid).toBe(true);
  });
});
```

**E2E Tests (Cypress):**
```typescript
// packages/client/cypress/e2e/chat.cy.ts
describe('Chat Flow', () => {
  it('should send and receive messages', () => {
    cy.visit('/chat/agent-id/channel-id');
    cy.get('[data-testid="message-input"]').type('Hello{enter}');
    cy.get('[data-testid="message-list"]')
      .should('contain', 'Hello');
  });
});
```

**Coverage:**
```bash
# Core package coverage
cd packages/core && bun test --coverage

# Client unit tests
cd packages/client && bun run test:unit

# E2E with server
cd packages/client && bun run test:e2e:with-server
```

### Документация

**Code Documentation:**
```typescript
/**
 * Process a message through the agent runtime
 * 
 * @param message - The memory object to process
 * @param state - Optional state override
 * @returns Array of response memories
 * 
 * @example
 * ```typescript
 * const responses = await runtime.processMessage(message);
 * ```
 */
async processMessage(message: Memory, state?: State): Promise<Memory[]>
```

**README Structure:**
- Quick start (5-minute setup)
- Feature highlights
- CLI commands reference
- Architecture overview
- Contribution guide

---

## 🔒 Безопасность

### Аутентификация

**API Key Authentication:**
```typescript
// Optional token-based auth
const serverAuthToken = process.env.ELIZA_SERVER_AUTH_TOKEN;
if (serverAuthToken && req.headers['x-api-key'] !== serverAuthToken) {
  return res.status(401).send('Unauthorized');
}
```

**Frontend Token Storage:**
```typescript
// localStorage для API ключей
const apiKey = localStorage.getItem('apiKey');
const client = new ElizaClient({ apiKey });
```

### Авторизация

**Row-Level Security в PostgreSQL:**
```sql
-- Каждый агент видит только свои данные
CREATE POLICY agents_owner_policy ON agents
  USING (owner_id = current_setting('app.current_owner_id')::uuid);
```

**Middleware проверки прав:**
```typescript
// Проверка существования агента
export const agentExistsMiddleware = async (req, res, next) => {
  const { agentId } = req.params;
  const agent = await getAgent(agentId);
  if (!agent) return res.status(404).send('Agent not found');
  next();
};
```

### Защита от уязвимостей

**XSS Protection:**
- React автоматически экранирует данные
- CSP headers через Helmet
- Валидация входных данных

**CSRF Protection:**
```typescript
// Socket.IO с CORS
const io = new Server(httpServer, {
  cors: {
    origin: process.env.CLIENT_URL || 'http://localhost:5173',
    methods: ['GET', 'POST'],
    credentials: true,
  },
});
```

**SQL Injection Protection:**
- Drizzle ORM с параметризованными запросами
- Никакого raw SQL без санитизации

**Rate Limiting:**
```typescript
// packages/server/src/middleware/rate-limit.ts
export const createApiRateLimit = () => rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 минут
  max: 100,                   // 100 requests per window
  standardHeaders: true,
  legacyHeaders: false,
});
```

### Секреты и переменные окружения

```bash
# .env.example
OPENAI_API_KEY=your_key_here
ELIZA_SERVER_AUTH_TOKEN=optional_api_key

# Drizzle ORM с поддержкой шифрования
POSTGRES_URL=postgresql://user:pass@localhost:5432/db
```

**Encryption utilities:**
```typescript
// packages/core/src/secrets.ts
export function encryptSecret(plaintext: string, key: string): string {
  // AES encryption implementation
}

export function decryptSecret(ciphertext: string, key: string): string {
  // AES decryption implementation
}
```

---

## 🔧 Ключевые компоненты

### 1. AgentRuntime (Core)

**Назначение**: Центральный оркестратор для выполнения агента, управляет плагинами, сервисами, state.

```typescript
// packages/core/src/runtime.ts
export class AgentRuntime implements IAgentRuntime {
  async processMessage(message: Memory, state?: State): Promise<Memory[]> {
    // 1. Load state
    state = state || await this.composeState(message);
    
    // 2. Evaluate actions
    const actions = await this.evaluateActions(message, state);
    
    // 3. Execute action chain
    const results: Memory[] = [];
    for (const action of actions) {
      const result = await action.handler(
        this, message, state, {}, 
        async (response) => {
          const memory = await this.createMemory(response);
          results.push(memory);
        }
      );
      
      // Merge result into state
      if (result?.values) {
        state = { ...state, ...result.values };
      }
    }
    
    return results;
  }
  
  async useModel<T extends ModelTypeName>(
    type: T, 
    params: ModelParamsMap[T]
  ): Promise<ModelResultMap[T]> {
    const handlers = this.models.get(type);
    if (!handlers?.length) throw new Error(`No model for ${type}`);
    return await handlers[0](this, params);
  }
}
```

**API:**
- `processMessage()`: Обработка входящего сообщения
- `useModel()`: Вызов LLM модели
- `getService<T>()`: Получение сервиса по имени
- `composeState()`: Сборка state с providers
- `registerAction()`, `registerProvider()`: Регистрация компонентов

---

### 2. BaseDrizzleAdapter (Database)

**Назначение**: Абстрактный адаптер для работы с PostgreSQL/PGLite через Drizzle ORM.

```typescript
// packages/plugin-sql/src/base.ts
export abstract class BaseDrizzleAdapter extends DatabaseAdapter<any> {
  // Создание памяти с embeddings
  async createMemory(memory: Memory): Promise<void> {
    await this.withDatabase(async () => {
      // Generate embedding if content exists
      const embedding = memory.content?.text 
        ? await this.generateEmbedding(memory.content.text)
        : null;
      
      await this.db.insert(memoryTable).values({
        id: memory.id,
        agentId: memory.agentId,
        roomId: memory.roomId,
        content: memory.content,
        embedding: embedding,
        metadata: memory.metadata,
        createdAt: new Date(),
      });
    });
  }
  
  // Semantic search по embeddings
  async searchMemoriesByEmbedding(
    embedding: number[],
    options: SearchOptions
  ): Promise<Memory[]> {
    return await this.withDatabase(async () => {
      return await this.db
        .select()
        .from(memoryTable)
        .where(eq(memoryTable.agentId, options.agentId))
        .orderBy(cosineDistance(memoryTable.embedding, embedding))
        .limit(options.count || 10);
    });
  }
}
```

**API:**
- `createMemory()`, `getMemory()`, `searchMemories()`
- `createAgent()`, `getAgent()`, `listAgents()`
- `createRoom()`, `getRoomsForAgent()`
- `withTransaction()`: Транзакции

---

### 3. Express Server (HTTP + WebSocket)

**Назначение**: HTTP REST API + Socket.IO WebSocket сервер.

```typescript
// packages/server/src/index.ts
export async function startServer(config: ServerConfig) {
  const app = express();
  const httpServer = http.createServer(app);
  const io = new SocketIOServer(httpServer);
  
  // Middleware
  app.use(helmet());
  app.use(cors());
  app.use(express.json());
  app.use(createApiRateLimit());
  
  // API routes
  const apiRouter = createApiRouter(agentServer);
  app.use('/api', apiKeyAuthMiddleware, apiRouter);
  
  // Static client
  if (shouldEnableWebUI()) {
    const clientPath = getClientDistPath();
    app.use(express.static(clientPath));
  }
  
  // Socket.IO
  setupSocketIO(io, agentServer);
  
  const port = await findAvailablePort(config.port || 3000);
  httpServer.listen(port, () => {
    logger.info(`Server running on http://localhost:${port}`);
  });
}
```

**API Endpoints:**
- `GET /api/agents` - Список агентов
- `POST /api/agents/:id/message` - Отправка сообщения
- `GET /api/agents/:id/channels/:channelId/messages` - История
- `POST /api/groups` - Создание групп

---

### 4. React Chat Component

**Назначение**: Real-time чат интерфейс с WebSocket.

```typescript
// packages/client/src/routes/chat.tsx (simplified)
export default function Chat() {
  const { agentId, channelId } = useParams();
  const [messages, setMessages] = useState<Message[]>([]);
  const socket = useSocket();
  
  // Load messages via REST
  const { data } = useQuery({
    queryKey: ['messages', agentId, channelId],
    queryFn: () => client.messages.list(agentId!, channelId!),
  });
  
  // Listen for new messages via Socket.IO
  useEffect(() => {
    socket.on('message:new', (msg: Message) => {
      setMessages(prev => [...prev, msg]);
    });
    return () => socket.off('message:new');
  }, [socket]);
  
  // Send message
  const sendMessage = useMutation({
    mutationFn: (text: string) => 
      client.messages.send(agentId!, channelId!, { text }),
    onSuccess: () => queryClient.invalidateQueries(['messages']),
  });
  
  return (
    <div className="flex flex-col h-full">
      <MessageList messages={messages} />
      <MessageInput onSend={sendMessage.mutate} />
    </div>
  );
}
```

---

### 5. Plugin Bootstrap (Default Actions)

**Назначение**: Базовые actions, providers, evaluators для всех агентов.

```typescript
// packages/plugin-bootstrap/src/index.ts
export const bootstrapPlugin: Plugin = {
  name: 'bootstrap',
  description: 'Core actions and providers',
  
  actions: [
    replyAction,        // REPLY - генерация ответа
    sendMessageAction,  // SEND_MESSAGE - отправка сообщений
    ignoreAction,       // IGNORE - игнорирование
    choiceAction,       // CHOICE - выбор из вариантов
    updateEntityAction, // UPDATE_ENTITY - обновление сущностей
    followRoomAction,   // FOLLOW_ROOM - подписка на канал
    muteRoomAction,     // MUTE_ROOM - отключение уведомлений
  ],
  
  providers: [
    timeProvider,       // TIME - текущее время
    factsProvider,      // FACTS - факты из памяти
    boredomProvider,    // BOREDOM - уровень "скуки"
    recentMessagesProvider, // RECENT_MESSAGES - история
  ],
  
  evaluators: [
    goalEvaluator,      // Оценка достижения целей
    factEvaluator,      // Извлечение фактов
  ],
  
  services: [
    TaskService,        // Управление задачами
    EmbeddingGenerationService, // Генерация embeddings
  ],
};
```

---

### 6. CLI (elizaos command)

**Назначение**: Command-line interface для управления агентами и проектами.

```typescript
// packages/cli/src/index.ts
import { Command } from 'commander';

const program = new Command();

program
  .name('elizaos')
  .version('1.6.4-alpha.18')
  .description('ElizaOS CLI - Manage AI agents');

// Create new project
program
  .command('create <name>')
  .description('Create a new ElizaOS project')
  .option('-t, --template <type>', 'Template type', 'project')
  .action(async (name, options) => {
    const spinner = ora('Creating project...').start();
    await scaffoldProject(name, options.template);
    spinner.succeed('Project created!');
  });

// Start agent
program
  .command('start')
  .description('Start ElizaOS server')
  .option('-p, --port <port>', 'Server port', '3000')
  .action(async (options) => {
    await startServer(options);
  });

// Run tests
program
  .command('test [path]')
  .option('-t, --type <type>', 'Test type', 'all')
  .action(async (path, options) => {
    await runTests(path, options.type);
  });

program.parse();
```

**Ключевые команды:**
- `elizaos create <name>` - Scaffold проекта
- `elizaos start` - Запуск сервера
- `elizaos dev` - Dev mode с watch
- `elizaos test` - Запуск тестов
- `elizaos agent list` - Список агентов
- `elizaos env edit-local` - Редактирование .env

---

### 7. API Client (Type-safe wrapper)

**Назначение**: Типизированный клиент для REST API.

```typescript
// packages/api-client/src/index.ts
export class ElizaClient {
  constructor(private config: ClientConfig) {}
  
  // Agent endpoints
  agents = {
    listAgents: async (): Promise<Agent[]> => {
      return this.request('GET', '/api/agents');
    },
    
    createAgent: async (data: CreateAgentDto): Promise<Agent> => {
      return this.request('POST', '/api/agents', data);
    },
    
    getAgent: async (id: string): Promise<Agent> => {
      return this.request('GET', `/api/agents/${id}`);
    },
  };
  
  // Message endpoints
  messages = {
    list: async (agentId: string, channelId: string) => {
      return this.request('GET', 
        `/api/agents/${agentId}/channels/${channelId}/messages`);
    },
    
    send: async (agentId: string, channelId: string, content: any) => {
      return this.request('POST', 
        `/api/agents/${agentId}/message`, 
        { channelId, content });
    },
  };
  
  private async request<T>(
    method: string, 
    path: string, 
    body?: any
  ): Promise<T> {
    const response = await fetch(`${this.config.baseUrl}${path}`, {
      method,
      headers: {
        'Content-Type': 'application/json',
        'X-API-KEY': this.config.apiKey || '',
      },
      body: body ? JSON.stringify(body) : undefined,
    });
    
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  }
}
```

---

### 8. BM25 Search (Text Search)

**Назначение**: BM25 алгоритм для поиска по тексту (дополняет vector search).

```typescript
// packages/core/src/search.ts
export class BM25 {
  constructor(
    private docs: string[],
    private k1: number = 1.2,
    private b: number = 0.75
  ) {
    this.preprocessDocuments();
  }
  
  search(query: string, topK: number = 10): SearchResult[] {
    const queryTokens = this.tokenize(query);
    const scores = this.docs.map((doc, idx) => ({
      index: idx,
      score: this.score(queryTokens, idx),
    }));
    
    return scores
      .sort((a, b) => b.score - a.score)
      .slice(0, topK);
  }
  
  private score(queryTokens: string[], docIndex: number): number {
    let score = 0;
    for (const token of queryTokens) {
      score += this.idf(token) * this.tf(token, docIndex);
    }
    return score;
  }
}
```

---

## 🚀 Производительность и масштабируемость

### Кеширование

**1. Turbo Remote Cache:**
```json
// turbo.json
{
  "tasks": {
    "build": {
      "outputs": ["dist/**"],
      "dependsOn": ["^build"]
    }
  }
}
```
- Кеширование build артефактов
- Shared cache для CI/CD
- Incremental builds

**2. TanStack Query Cache:**
```typescript
// Client-side caching
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 30000,      // 30 seconds
      cacheTime: 5 * 60000,  // 5 minutes
      retry: 3,
    },
  },
});
```

**3. State Cache в Runtime:**
```typescript
// AgentRuntime кеширует состояние
stateCache = new Map<string, State>();

async composeState(message: Memory): Promise<State> {
  const cacheKey = `${message.agentId}-${message.roomId}`;
  if (this.stateCache.has(cacheKey)) {
    return this.stateCache.get(cacheKey)!;
  }
  
  const state = await this.buildState(message);
  this.stateCache.set(cacheKey, state);
  return state;
}
```

### Database Optimization

**1. Indexes:**
```sql
-- Vector similarity index
CREATE INDEX memories_embedding_idx 
  ON memories USING ivfflat (embedding vector_cosine_ops);

-- Text search indexes
CREATE INDEX memories_agent_id_idx ON memories(agent_id);
CREATE INDEX memories_room_id_idx ON memories(room_id);
CREATE INDEX memories_created_at_idx ON memories(created_at DESC);
```

**2. Connection Pooling:**
```typescript
// PostgreSQL pool
const pool = new Pool({
  max: 20,
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});
```

**3. Batching:**
```typescript
// Batch embeddings generation
async generateEmbeddings(texts: string[]): Promise<number[][]> {
  const BATCH_SIZE = 10;
  const results = [];
  
  for (let i = 0; i < texts.length; i += BATCH_SIZE) {
    const batch = texts.slice(i, i + BATCH_SIZE);
    const embeddings = await this.embeddingService.embed(batch);
    results.push(...embeddings);
  }
  
  return results;
}
```

### Code Splitting (Frontend)

```typescript
// vite.config.ts - Manual chunks
build: {
  rollupOptions: {
    output: {
      manualChunks: (id) => {
        if (id.includes('react')) return 'react-vendor';
        if (id.includes('@radix-ui')) return 'ui-vendor';
        if (id.includes('@elizaos')) return 'elizaos-vendor';
      },
    },
  },
}
```

### Concurrency Control

**Semaphore для ограничения параллельных операций:**
```typescript
// packages/core/src/runtime.ts
export class Semaphore {
  private permits: number;
  private waiting: Array<() => void> = [];
  
  constructor(count: number) {
    this.permits = count;
  }
  
  async acquire(): Promise<void> {
    if (this.permits > 0) {
      this.permits -= 1;
      return;
    }
    return new Promise<void>((resolve) => {
      this.waiting.push(resolve);
    });
  }
  
  release(): void {
    this.permits += 1;
    const next = this.waiting.shift();
    if (next && this.permits > 0) {
      this.permits -= 1;
      next();
    }
  }
}

// Использование
const semaphore = new Semaphore(3); // Max 3 параллельных
await semaphore.acquire();
try {
  await heavyOperation();
} finally {
  semaphore.release();
}
```

---

## 🔌 Внешние интеграции

### AI Model Providers

- **OpenAI** (GPT-4, GPT-3.5): `OPENAI_API_KEY`
- **Anthropic** (Claude): `ANTHROPIC_API_KEY`
- **Google** (Gemini): `GOOGLE_GENERATIVE_AI_API_KEY`
- **Grok** (xAI): `XAI_API_KEY`
- **Local Models**: Llama через node-llama-cpp

### Social Platforms

- **Discord**: Bot API (`DISCORD_APPLICATION_ID`, `DISCORD_API_TOKEN`)
- **Telegram**: Bot API (`TELEGRAM_BOT_TOKEN`)
- **Twitter/X**: API v2 (`TWITTER_*`)
- **Farcaster**: Decentralized social

### Blockchain

- **EVM Chains**: Ethereum, Polygon, BSC (`EVM_PRIVATE_KEY`)
- **Solana**: Native integration (`SOLANA_PRIVATE_KEY`)
- **Wallet Services**: Multi-chain wallet management

### Infrastructure

- **Sentry**: Error tracking (`SENTRY_DSN`)
- **Database**: PostgreSQL with pgvector (`POSTGRES_URL`)
- **Storage**: Local filesystem + optional cloud storage

### Analytics

- Built-in logging system (adze logger)
- Custom analytics через plugin system

---

## 📦 DevOps и развертывание

### Docker Configuration

**Multi-stage Build:**
```dockerfile
# Stage 1: Builder
FROM node:23.3.0-slim AS builder
RUN npm install -g bun@1.2.21 turbo@2.3.3
COPY package.json turbo.json tsconfig.json ./
COPY packages ./packages
RUN bun install --no-cache
RUN TURBO_CONCURRENCY=2 bun run build --concurrency=2

# Stage 2: Production
FROM node:23.3.0-slim
RUN npm install -g bun@1.2.21
COPY --from=builder /app/packages ./packages
COPY --from=builder /app/node_modules ./node_modules
ENV NODE_ENV=production
EXPOSE 3000
CMD ["bun", "run", "start"]
```

**Docker Compose:**
```yaml
services:
  postgres:
    image: ankane/pgvector:latest
    environment:
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: eliza
    volumes:
      - postgres-data:/var/lib/postgresql/data
    ports:
      - '5432:5432'
    healthcheck:
      test: pg_isready -U postgres
      interval: 5s
  
  eliza:
    build: .
    depends_on:
      postgres:
        condition: service_healthy
    environment:
      - POSTGRES_URL=${POSTGRES_URL}
      - OPENAI_API_KEY=${OPENAI_API_KEY}
    ports:
      - '3000:3000'
```

### CI/CD Pipeline (GitHub Actions)

```yaml
# .github/workflows/ci.yaml
name: ci
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: oven-sh/setup-bun@v2
      - run: bun install
      - run: cd packages/core && bun test:coverage
  
  lint-and-format:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: oven-sh/setup-bun@v2
      - run: bun install
      - run: bun run format:check
      - run: bun run lint
  
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: oven-sh/setup-bun@v2
      - run: bun install
      - run: bun run build
```

### NPM Scripts

```json
{
  "scripts": {
    "start": "turbo run start --filter=./packages/cli",
    "dev": "bun run scripts/dev-watch.js",
    "build": "turbo run build",
    "test": "turbo run test --concurrency 3",
    "lint": "turbo run lint && prettier --write .",
    "release": "lerna publish from-package --dist-tag latest",
    "docker:build": "bash ./scripts/docker.sh build",
    "docker:run": "bash ./scripts/docker.sh run"
  }
}
```

### Environment Configuration

**Development:**
```bash
# .env.local
NODE_ENV=development
LOG_LEVEL=debug
POSTGRES_URL=postgresql://localhost:5432/eliza_dev
OPENAI_API_KEY=sk-...
```

**Production:**
```bash
# .env.production
NODE_ENV=production
LOG_LEVEL=info
POSTGRES_URL=postgresql://prod-db:5432/eliza
ELIZA_SERVER_AUTH_TOKEN=secure_token_here
```

### Deployment Strategies

**Local Development:**
```bash
bun install
bun run build
bun run start
```

**Docker:**
```bash
docker-compose up -d
```

**Cloud (Managed):**
- Vercel/Netlify для client (static)
- Railway/Render для server
- Supabase/Neon для Postgres

---

## 📊 Выводы и рекомендации

### Сильные стороны

1. **🎯 Продуманная архитектура**
   - Четкое разделение на core/server/client/cli
   - Мощная plugin система с Actions/Services/Providers/Evaluators
   - Model-agnostic AI интеграция

2. **⚡ Современный технологический стек**
   - Bun для максимальной производительности
   - React 19 + Vite для быстрого UI
   - Drizzle ORM с type-safety
   - PostgreSQL + pgvector для semantic search

3. **🧩 Модульность и расширяемость**
   - Легко добавлять новые плагины
   - Workspace dependencies для монорепо
   - Хорошо документированные интерфейсы

4. **🔒 Безопасность**
   - Row-Level Security в БД
   - Rate limiting на API
   - Helmet для security headers
   - API key authentication

5. **📦 Developer Experience**
   - Понятный CLI
   - Горячая перезагрузка
   - Turbo для быстрых builds
   - Comprehensive documentation

6. **🧪 Тестирование**
   - Unit тесты с Bun test
   - E2E тесты с Cypress
   - Integration тесты для server
   - Coverage reporting

---

### Области для улучшения

#### Высокий приоритет

1. **📝 Type Safety**
   - **Проблема**: Некоторые места используют `any`, `unknown`
   - **Решение**: Строгая типизация всех интерфейсов, избегание any
   - **Пример**: ActionResult, HandlerOptions могут быть более строгими

2. **🧪 Test Coverage**
   - **Проблема**: Не все критичные компоненты покрыты тестами
   - **Решение**: Добавить тесты для API endpoints, middleware, critical services
   - **Цель**: Минимум 80% coverage для core/server

3. **📖 Документация**
   - **Проблема**: API endpoints недостаточно документированы
   - **Решение**: Swagger/OpenAPI спецификация
   - **Инструмент**: typedoc + openapi-generator

#### Средний приоритет

4. **⚡ Performance**
   - **Database**: Добавить prepared statements, optimize queries
   - **Frontend**: Lazy load components, virtual scrolling для больших списков
   - **Memory**: Лимиты на stateCache, periodic cleanup

5. **🔍 Observability**
   - **Logging**: Структурированные логи (JSON)
   - **Metrics**: Prometheus metrics export
   - **Tracing**: OpenTelemetry integration

6. **🌐 Internationalization**
   - **UI**: i18next для мультиязычности
   - **Character**: Поддержка разных языков для агентов

#### Низкий приоритет

7. **🎨 UI/UX**
   - Добавить темы (light/dark mode toggle)
   - Улучшить mobile experience
   - Accessibility audit (WCAG 2.1)

8. **📦 Bundle Size**
   - Анализ и оптимизация bundle размера
   - Tree-shaking неиспользуемого кода
   - Dynamic imports для редких features

9. **🔄 Graceful Degradation**
   - Fallback UI при отсутствии WebSocket
   - Offline mode для client
   - Retry mechanisms с exponential backoff

---

### Уровень сложности

**Senior-friendly** (60%):
- Сложная архитектура с плагинами и runtime оркестрацией
- Требует понимания monorepo, build tools, async patterns
- Plugin система с dependency resolution
- Vector search + semantic embeddings
- Multi-agent coordination

**Middle-friendly** (30%):
- React компоненты с hooks
- REST API endpoints
- Drizzle ORM queries
- CLI commands
- Unit тесты

**Junior-friendly** (10%):
- Простые UI компоненты
- Styling с Tailwind
- Документация
- Примеры и tutorials

**Вердикт**: Проект требует **Middle+/Senior уровня** для core contributions, но Middle разработчики могут работать над отдельными фичами с mentoring.

---

### Следующие шаги (Roadmap)

#### Немедленно (0-1 месяц)

1. **Улучшить test coverage** до 80%+ для core/server
2. **Добавить OpenAPI spec** для REST API
3. **Оптимизировать database queries** (indexes, prepared statements)
4. **Документировать plugin creation** с примерами

#### Краткосрочно (1-3 месяца)

5. **Добавить Prometheus metrics** для мониторинга
6. **Реализовать graceful shutdown** для server
7. **UI improvements**: темизация, mobile optimization
8. **Performance audit**: profiling, benchmarking

#### Среднесрочно (3-6 месяцев)

9. **Horizontal scaling**: поддержка нескольких server instances
10. **Plugin marketplace**: публичный registry плагинов
11. **Advanced RAG**: hybrid search (BM25 + vector)
12. **Multi-modal support**: images, audio, video

#### Долгосрочно (6+ месяцев)

13. **Cloud-native deployment**: Kubernetes charts, Helm
14. **Enterprise features**: SSO, RBAC, audit logs
15. **Advanced analytics**: dashboard для metrics
16. **AI orchestration**: multi-agent workflows

---

## 🎓 Рекомендации для новых разработчиков

### Начало работы

1. **Изучить документацию**:
   - README.md - Quick start
   - CLAUDE.md - Development guidelines
   - AGENTS.md - Agent concepts

2. **Настроить окружение**:
   ```bash
   # Установка Bun
   curl -fsSL https://bun.sh/install | bash
   
   # Клонирование и setup
   git clone https://github.com/elizaos/eliza.git
   cd eliza
   bun install
   bun run build
   ```

3. **Запустить в dev mode**:
   ```bash
   # Terminal 1: Server
   bun run start
   
   # Terminal 2: Client (optional)
   cd packages/client
   bun run dev
   ```

4. **Изучить примеры**:
   - `examples/` - Standalone примеры
   - `packages/plugin-starter/` - Шаблон плагина
   - `packages/project-starter/` - Шаблон проекта

### Архитектурные принципы

- **Всегда используй Bun** (не npm, не node)
- **Workspace dependencies** (`workspace:*`)
- **Bun.spawn()** для process execution (не Node.js APIs)
- **EventTarget** для событий (не EventEmitter)
- **Bun test** для тестирования (не Jest)

### Code Style

- **Prettier** для форматирования (auto-fix)
- **camelCase** для переменных/функций
- **PascalCase** для types/components
- **Строгая типизация** - избегать `any`

### Тестирование

```bash
# Unit тесты
cd packages/core
bun test

# E2E тесты
cd packages/client
bun run test:e2e

# Coverage
bun test --coverage
```

---

## 📚 Полезные ссылки

- **Репозиторий**: https://github.com/elizaos/eliza
- **Документация**: https://docs.elizaos.ai/
- **Discord**: https://discord.gg/ai16z
- **NPM Package**: https://www.npmjs.com/package/@elizaos/cli
- **Bun Documentation**: https://bun.sh/docs
- **Drizzle ORM**: https://orm.drizzle.team/
- **Radix UI**: https://www.radix-ui.com/

---

**Дата анализа**: 2025  
**Версия проекта**: 1.6.4-alpha.18  
**Анализатор**: Claude (Anthropic)
