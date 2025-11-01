# Анализ фронтенд кодовой базы: ElizaOS Client

## 📁 Структура проекта

### Обзор архитектуры

ElizaOS представляет собой **монорепозиторий**, управляемый через **Turbo** и **Lerna**, где фронтенд-часть находится в пакете `packages/client`. Проект построен на основе современного React-стека с использованием TypeScript и следует принципам **feature-based архитектуры** с элементами **layer-based подхода**.

### Дерево директорий (3 уровня)

```
packages/client/
├── src/
│   ├── components/           # UI-компоненты и бизнес-логика
│   │   ├── ui/              # Базовые UI-примитивы (кнопки, диалоги, формы)
│   │   ├── agent-prism/     # Визуализация трассировки агентов
│   │   ├── agent-runs/      # Компоненты запусков агентов
│   │   └── ai-elements/     # AI-специфичные элементы интерфейса
│   ├── routes/              # Компоненты страниц/маршрутов
│   ├── hooks/               # Кастомные React-хуки
│   │   └── __tests__/       # Unit-тесты для хуков
│   ├── context/             # React Context провайдеры
│   ├── lib/                 # Утилиты и вспомогательные функции
│   ├── types/               # TypeScript типы и интерфейсы
│   ├── config/              # Конфигурационные файлы
│   ├── mocks/               # Моки для тестирования
│   └── test/                # Тестовые утилиты
├── cypress/                 # E2E и компонентные тесты
│   ├── e2e/                # End-to-end тесты
│   ├── component/          # Компонентные тесты
│   └── support/            # Вспомогательные файлы для Cypress
├── public/                  # Статические ассеты
└── scripts/                 # Скрипты автоматизации
```

### Назначение ключевых директорий

**`src/components/`** - Центральная директория со всеми React-компонентами. Разделена на поддиректории: `ui/` содержит переиспользуемые UI-примитивы на основе Radix UI, остальные поддиректории группируют компоненты по фичам (агенты, чаты, настройки).

**`src/routes/`** - Компоненты верхнего уровня, соответствующие маршрутам приложения (Home, Chat, Settings). Связаны с React Router и служат точками входа для различных страниц.

**`src/hooks/`** - Кастомные хуки для инкапсуляции бизнес-логики, управления состоянием и взаимодействия с API. Включает хуки для работы с агентами, сообщениями, серверами и UI-состояниями.

**`src/context/`** - React Context провайдеры для глобального состояния: AuthContext (аутентификация), ConnectionContext (WebSocket соединение).

**`src/lib/`** - Вспомогательные функции, утилиты, конфигурация API-клиента, логирование, Socket.IO менеджер, маппинг типов между API и клиентом.

**`cypress/`** - Полноценная тестовая инфраструктура с E2E и компонентными тестами, обеспечивающая высокий уровень покрытия критичных пользовательских сценариев.

### Принципы организации кода

Проект следует **гибридной архитектуре**:

1. **Feature-based** - Компоненты сгруппированы по функциональности (agent-*, group-*, chat-*)
2. **Layer-based** - Четкое разделение на слои: UI (components), Business Logic (hooks), Data Layer (lib/api-client), State Management (context + TanStack Query)
3. **Atomic Design principles** - UI-компоненты организованы от простых примитивов (button, input) к сложным композициям (chat, agent-creator)

---

## 🛠 Технологический стек

### Основной стек

**Фреймворк:** React 19.1.0 (новейшая версия с улучшенной производительностью)  
**Язык:** TypeScript 5.9.2 (strict mode включен)  
**Сборщик:** Vite 7.1.7 (modern ESM-first build tool)  
**Пакетный менеджер:** Bun 1.2.21 (используется во всем монорепозитории)

### CSS и стилизация

**Подход:** Utility-first с Tailwind CSS 4.1.0  
**Плагины:** @tailwindcss/vite 4.1.0, tailwindcss-animate 1.0.7  
**Дизайн-система:** Radix UI (набор компонентов-примитивов)  
**CSS-in-JS:** Нет, используется чистый Tailwind с CSS-переменными для темизации  
**PostCSS:** Минимальная конфигурация (postcss.config.js)

### Управление состоянием

**Серверное состояние:** TanStack Query 5.67.2 (React Query) - основной инструмент  
**Глобальное состояние:** React Context API (AuthContext, ConnectionContext)  
**Локальное состояние:** React Hooks (useState, useReducer)  
**WebSocket состояние:** Кастомный SocketIOManager с использованием библиотеки Evt 2.5.9

### Роутинг

**React Router 7.3.0** (react-router + react-router-dom) - новейшая версия с улучшенным Data API

### API и работа с данными

**API Client:** @elizaos/api-client (workspace package) - типобезопасный клиент  
**HTTP клиент:** Встроен в @elizaos/api-client  
**WebSocket:** Socket.IO Client 4.8.1 для real-time коммуникации  
**Кеширование:** TanStack Query с настраиваемыми stale times и умными стратегиями refetch

### UI библиотеки

**Radix UI** - Полный набор компонентов-примитивов:
- @radix-ui/react-dialog 1.1.6
- @radix-ui/react-dropdown-menu 2.1.15
- @radix-ui/react-tabs 1.1.3
- @radix-ui/react-toast 1.2.6
- @radix-ui/react-select 2.1.6
- И другие (всего ~15 пакетов)

**Дополнительные библиотеки:**
- lucide-react 0.544.0 - Иконки
- class-variance-authority 0.7.1 - Управление вариантами компонентов
- clsx 2.1.1 + tailwind-merge 3.3.1 - Утилиты для классов
- react-hook-form 7.x (через @hookform/resolvers 5.1.1) - Формы
- date-fns 4.1.0 + dayjs 1.11.13 - Работа с датами

### Специализированные библиотеки

**Визуализация:**
- react-force-graph 1.47.6 - Графовые визуализации
- vis-timeline 8.3.1 - Временные шкалы
- shiki 3.6.0 - Подсветка синтаксиса

**AI/Агенты:**
- @evilmartians/agent-prism-data 0.0.4 - Трассировка AI-агентов
- react-aiwriter 1.0.0 - AI-генерация текста
- streamdown 1.4.0 - Markdown streaming

**UX:**
- react-joyride 2.9.3 - Онбординг туры
- react-resizable-panels 3.0.6 - Ресизейбл панели

### Тестирование

**Unit тесты:** Bun test (встроенный test runner)  
**E2E тесты:** Cypress 15.3.0  
**Компонентные тесты:** Cypress Component Testing  
**Test utilities:**
- @testing-library/react 16.3.0
- @testing-library/cypress 10.0.3
- @happy-dom/global-registrator 19.0.2
- cypress-real-events 1.14.0

**Coverage:** Поддержка через bun test --coverage

### Качество кода

**Линтер:** ESLint 9.22.0 с TypeScript конфигурацией  
**Форматтер:** Prettier 3.6.2  
**Плагины ESLint:**
- @typescript-eslint/eslint-plugin 8.45.0
- eslint-plugin-react 7.37.4
- eslint-plugin-react-hooks 5.2.0
- eslint-plugin-jsx-a11y 6.10.2 (доступность)
- eslint-plugin-import 2.31.0

### Конфигурация сборки

**Vite конфиг особенности:**
- React SWC plugin для быстрой компиляции
- Оптимизация chunk-splitting (react-vendor, ui-vendor, elizaos-vendor)
- Sourcemaps включены в production для лучшей отладки
- Proxy для API (/api → localhost:3000)
- WebSocket proxy (/socket.io)

**TypeScript конфиг:**
```json
{
  "target": "ES2022",
  "module": "ESNext",
  "moduleResolution": "bundler",
  "strict": true,
  "jsx": "react-jsx"
}
```

### Версии ключевых зависимостей

| Зависимость | Версия | Назначение |
|-------------|--------|------------|
| react | 19.1.0 | UI фреймворк |
| typescript | 5.9.2 | Язык разработки |
| vite | 7.1.7 | Сборщик |
| @tanstack/react-query | 5.67.2 | Управление серверным состоянием |
| react-router-dom | 7.3.0 | Роутинг |
| tailwindcss | 4.1.0 | CSS фреймворк |
| socket.io-client | 4.8.1 | WebSocket коммуникация |
| @elizaos/core | workspace:* | Ядро ElizaOS |
| @elizaos/api-client | workspace:* | API клиент |

---

## 🏗 Архитектура

### Компонентная архитектура

Приложение построено на **функциональных компонентах** с использованием React Hooks. Применяется паттерн **Composition over Inheritance** с активным использованием Render Props и Component Composition.

#### Разделение логики

**Custom Hooks** - Основной способ переиспользования логики:

```typescript
// Пример: hooks/use-query-hooks.ts
export function useAgent(agentId: UUID | undefined | null, options = {}) {
  const network = useNetworkStatus();

  return useQuery<{ data: AgentWithStatus }>({
    queryKey: ['agent', agentId],
    queryFn: async () => {
      if (!agentId) throw new Error('Agent ID is required');
      const result = await elizaClient.agents.getAgent(agentId);
      return { data: mapApiAgentToClient(result) };
    },
    staleTime: STALE_TIMES.FREQUENT,
    enabled: Boolean(agentId),
    refetchInterval: !network.isOffline && Boolean(agentId) 
      ? STALE_TIMES.FREQUENT 
      : false,
    ...options,
  });
}
```

**Композиция через Radix Slot:**

```typescript
// components/ui/button.tsx
function Button({ asChild = false, ...props }) {
  const Comp = asChild ? Slot : 'button';
  return <Comp className={cn(buttonVariants({ variant, size }))} {...props} />;
}
```

**Variants через class-variance-authority:**

```typescript
const buttonVariants = cva(
  "inline-flex items-center justify-center gap-2...",
  {
    variants: {
      variant: {
        default: 'bg-primary text-primary-foreground',
        destructive: 'bg-red-800 text-white',
        ghost: 'hover:bg-accent',
      },
      size: { default: 'h-9 px-4', sm: 'h-8 px-3', lg: 'h-10 px-8' },
    },
  }
);
```

### Управление состоянием

**Трехуровневая архитектура:**

1. **Серверное состояние** (TanStack Query) - агенты, сообщения, серверы
2. **Глобальное клиентское состояние** (Context) - аутентификация, соединение
3. **Локальное UI состояние** (useState, useReducer) - формы, модальные окна

**Умное кеширование с адаптацией к сети:**

```typescript
export const STALE_TIMES = {
  FREQUENT: 30000,    // 30 сек - часто меняющиеся данные
  STANDARD: 120000,   // 2 мин - стандартные данные
  RARE: 600000,       // 10 мин - редко меняющиеся данные
  NEVER: Number.POSITIVE_INFINITY,
};

// Адаптация частоты опроса к качеству сети
const useNetworkStatus = () => {
  const connection = navigator.connection;
  return {
    isOffline: !navigator.onLine,
    effectiveType: connection?.effectiveType || 'unknown',
    saveData: connection?.saveData || false,
  };
};
```

**Optimistic Updates:**

```typescript
export function useStartAgent() {
  return useMutation({
    mutationFn: async (agentId: UUID) => {
      const result = await elizaClient.agents.startAgent(agentId);
      return { data: { id: agentId, status: result.status } };
    },
    onMutate: async () => {
      toast({ title: 'Starting Agent', description: 'Initializing...' });
    },
    onSuccess: (response, agentId) => {
      queryClient.invalidateQueries({ queryKey: ['agents'] });
      queryClient.invalidateQueries({ queryKey: ['agent', agentId] });
      toast({ title: 'Agent Started' });
    },
  });
}
```

### API-слой и работа с данными

**Singleton паттерн для API клиента:**

```typescript
// lib/api-client-config.ts
let elizaClientInstance: ElizaClient | null = null;

export function createElizaClient(): ElizaClient {
  if (!elizaClientInstance) {
    elizaClientInstance = ElizaClient.create(createApiClientConfig());
  }
  return elizaClientInstance;
}

export function resetElizaClient(): void {
  elizaClientInstance = null;
}
```

**Маппинг типов между API и клиентом:**

Используется отдельный модуль `lib/api-type-mappers.ts` для преобразования типов из API-ответов в клиентские типы, обеспечивая type safety и согласованность данных.

**Стратегия обработки ошибок:**

- Централизованная обработка через TanStack Query
- Retry с экспоненциальной задержкой (3 попытки)
- Пользовательские уведомления через toast
- Graceful degradation при отсутствии сети

### Роутинг и навигация

**React Router v7 с вложенными маршрутами:**

```typescript
<Routes>
  <Route path="/" element={<Home key={homeKey} />} />
  <Route path="chat/:agentId/:channelId" element={<Chat />} />
  <Route path="chat/:agentId" element={<Chat />} />
  <Route path="settings/:agentId" element={<AgentSettingsRoute />} />
  <Route path="group/new" element={<CreateGroupPage />} />
  <Route path="group/:channelId" element={<GroupChannel />} />
  <Route path="agents/new" element={<AgentCreatorRoute />} />
  <Route path="*" element={<NotFound />} />
</Routes>
```

**Динамическое обновление через key prop:**

```typescript
const [homeKey, setHomeKey] = useState(Date.now());

const refreshHomePage = () => {
  setHomeKey(Date.now());
  queryClient.invalidateQueries({ queryKey: ['agents'] });
};

<Route path="/" element={<Home key={homeKey} />} />
```

### Обработка ошибок и состояний загрузки

**Connection Error Banner:**

```typescript
export const ConnectionErrorBanner = () => {
  const { status, error } = useConnection();
  
  if (status !== 'error') return null;
  
  return (
    <Alert variant="destructive">
      <AlertTitle>Connection Error</AlertTitle>
      <AlertDescription>{error}</AlertDescription>
    </Alert>
  );
};
```

**Условный рендеринг в маршрутах:**

```typescript
export default function AgentRoute() {
  const { agentId } = useParams();
  const { data, isLoading } = useAgent(agentId);

  if (!agentId) return <div>Agent ID not provided.</div>;
  if (isLoading) return <Loader2 className="animate-spin" />;
  
  const isActive = data?.status === CoreAgentStatusEnum.ACTIVE;
  
  if (!isActive) {
    return (
      <div className="flex flex-col items-center">
        <h2>{data.name} is not active.</h2>
        <Button onClick={handleStartAgent}>Start Agent</Button>
      </div>
    );
  }

  return <ChatComponent />;
}
```

### Real-time коммуникация

**SocketIOManager - Singleton с EventAdapter:**

```typescript
// lib/socketio-manager.ts
class EventAdapter {
  private events: Record<string, Evt<any>> = {};

  on(eventName: string, listener: Function) {
    if (!this.events[eventName]) {
      this.events[eventName] = Evt.create();
    }
    this.events[eventName].attach(listener);
  }

  emit(eventName: string, ...args: any[]) {
    if (this.events[eventName]) {
      this.events[eventName].post(args[0]);
    }
  }
}

export class SocketIOManager extends EventAdapter {
  private static instance: SocketIOManager | null = null;
  private socket: Socket | null = null;
  
  public static getInstance(): SocketIOManager {
    if (!SocketIOManager.instance) {
      SocketIOManager.instance = new SocketIOManager();
    }
    return SocketIOManager.instance;
  }
}
```

**Интеграция с React Context:**

```typescript
export const ConnectionProvider = ({ children }) => {
  const [status, setStatus] = useState<ConnectionStatusType>('loading');
  const socketManager = SocketIOManager.getInstance();

  useEffect(() => {
    const onConnect = () => {
      setStatus('connected');
      toast({ title: 'Connection Restored' });
    };

    const onDisconnect = (reason: string) => {
      setStatus('error');
      toast({ title: 'Connection Lost', variant: 'destructive' });
    };

    socketManager.on('connect', onConnect);
    socketManager.on('disconnect', onDisconnect);

    return () => {
      socketManager.off('connect', onConnect);
      socketManager.off('disconnect', onDisconnect);
    };
  }, [socketManager]);

  return (
    <ConnectionContext.Provider value={{ status }}>
      {children}
    </ConnectionContext.Provider>
  );
};
```

---

## 🎨 UI/UX и стилизация

### Подход к стилизации

**Utility-first с Tailwind CSS 4.1.0** - Современный подход с использованием атомарных классов. Конфигурация использует **CSS-переменные** для темизации, что обеспечивает гибкость и поддержку динамической смены тем.

**Конфигурация Tailwind:**

```typescript
// tailwind.config.ts
export default {
  darkMode: ['class'],
  content: ['src/**/*.{ts,tsx}'],
  theme: {
    extend: {
      fontFamily: {
        sans: ['Inter', 'ui-sans-serif', 'system-ui'],
      },
      colors: {
        background: 'hsl(var(--background))',
        foreground: 'hsl(var(--foreground))',
        primary: {
          DEFAULT: 'hsl(var(--primary))',
          foreground: 'hsl(var(--primary-foreground))',
        },
        // ... дополнительные цветовые переменные
      },
    },
  },
  plugins: [tailwindAnimate],
};
```

### Дизайн-система

**Radix UI как UI Kit** - Проект использует Radix UI Primitives в качестве основы для компонентной библиотеки. Это обеспечивает:

- **Accessibility из коробки** - WAI-ARIA совместимость
- **Headless компоненты** - Полный контроль над стилями
- **Композируемость** - Гибкая структура компонентов

**Кастомная обертка компонентов:**

Все Radix компоненты обернуты в кастомные компоненты в директории `src/components/ui/`, добавляя единый стиль и дополнительную функциональность.

**Пример Button компонента:**

```typescript
const buttonVariants = cva(
  "inline-flex items-center justify-center gap-2 rounded-md...",
  {
    variants: {
      variant: {
        default: 'bg-primary text-primary-foreground shadow-sm',
        destructive: 'bg-red-800 text-white hover:bg-red-600',
        outline: 'border border-input bg-background',
        ghost: 'hover:bg-accent hover:text-accent-foreground',
      },
      size: {
        default: 'h-9 px-4',
        sm: 'h-8 px-3 text-xs',
        lg: 'h-10 px-8',
        icon: 'h-auto w-auto',
      },
    },
  }
);
```

### Адаптивность

**Mobile-first подход** - Используются breakpoint-модификаторы Tailwind:

```typescript
<SidebarInset className="h-screen flex flex-col md:ml-72 overflow-hidden">
  <div className="md:hidden absolute top-4 left-4 z-50">
    <Sheet open={mobileMenuOpen}>
      <SheetTrigger>
        <Button variant="ghost" size="icon">
          <Menu className="h-5 w-5" />
        </Button>
      </SheetTrigger>
    </Sheet>
  </div>
  <div className="flex w-full justify-center px-4 sm:px-6">
    <div className="w-full md:max-w-4xl">
      {/* Контент */}
    </div>
  </div>
</SidebarInset>
```

**Адаптивная навигация:**
- Desktop: Постоянный sidebar
- Mobile: Выдвижная Sheet-панель через Radix Dialog

### Темизация

**Dark-first дизайн** с использованием CSS-переменных в формате HSL:

```css
/* index.css */
:root {
  --background: 0 0% 100%;
  --foreground: 222.2 84% 4.9%;
  --primary: 222.2 47.4% 11.2%;
  /* ... */
}

.dark {
  --background: 222.2 84% 4.9%;
  --foreground: 210 40% 98%;
  --primary: 210 40% 98%;
  /* ... */
}
```

**Применение в компонентах:**

```typescript
<div className="dark antialiased font-sans" style={{ colorScheme: 'dark' }}>
  {/* App контент */}
</div>
```

### Доступность (a11y)

**Высокий уровень поддержки a11y:**

1. **ESLint plugin jsx-a11y** - Статическая проверка доступности
2. **Radix UI** - WAI-ARIA паттерны из коробки
3. **Семантические HTML элементы** - Правильное использование `<button>`, `<nav>`, `<main>`
4. **Keyboard navigation** - Поддержка клавиатурной навигации
5. **Screen reader support** - Использование `sr-only` классов и ARIA-атрибутов

**Пример accessible компонента:**

```typescript
<Button variant="ghost" size="icon" data-testid="mobile-menu-button">
  <Menu className="h-5 w-5" />
  <span className="sr-only">Toggle menu</span>
</Button>
```

### Анимации и переходы

**Tailwind Animate plugin** для базовых анимаций:

```typescript
// tailwind.config.ts
animation: {
  'bounce-sm': 'bounce-sm 0.6s ease-in-out 2',
},
keyframes: {
  'bounce-sm': {
    '0%, 100%': { transform: 'translateY(0)' },
    '50%': { transform: 'translateY(-4px)' },
  },
},
```

**React Spring** для сложных анимаций:
- Используется библиотека @react-spring/web 10.0.3
- Применяется для плавных переходов в графовых визуализациях

### UX паттерны

**Onboarding Tour** - react-joyride для гайдов по первому использованию:

```typescript
<OnboardingTour />
```

**Toast notifications** - Radix Toast для уведомлений:

```typescript
const { toast } = useToast();

toast({
  title: 'Agent Started',
  description: 'Agent is now running',
});
```

**Resizable Panels** - react-resizable-panels для гибких layouts:

```typescript
<ResizablePanelGroup direction="horizontal">
  <ResizablePanel defaultSize={60}>
    <ChatComponent />
  </ResizablePanel>
  <ResizableHandle />
  <ResizablePanel defaultSize={40}>
    <AgentDetailsPanel />
  </ResizablePanel>
</ResizablePanelGroup>
```

---

## ✅ Качество кода

### Конфигурация линтеров

**ESLint 9.22.0** с TypeScript и React плагинами:

Конфигурация находится в `package.json` devDependencies. Используются следующие плагины:
- @typescript-eslint/eslint-plugin 8.45.0
- @typescript-eslint/parser 8.45.0
- eslint-plugin-react 7.37.4
- eslint-plugin-react-hooks 5.2.0
- eslint-plugin-jsx-a11y 6.10.2
- eslint-plugin-import 2.31.0

**Prettier 3.6.2** конфигурация:

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

**Скрипты качества:**

```json
{
  "lint": "prettier --write ./src",
  "format": "prettier --write ./src",
  "format:check": "prettier --check ./src"
}
```

### Качество типизации TypeScript

**Строгий режим TypeScript включен:**

```json
{
  "strict": true,
  "noUnusedLocals": false,
  "noUnusedParameters": false,
  "noFallthroughCasesInSwitch": true
}
```

**Высокий уровень типизации:**

- Все API-ответы типизированы через @elizaos/api-client
- Используются дженерики для переиспользуемых хуков
- Type guards для runtime проверок
- Discriminated unions для состояний

**Примеры качественной типизации:**

```typescript
export type ConnectionStatusType =
  | 'loading'
  | 'connected'
  | 'reconnecting'
  | 'error'
  | 'unauthorized';

interface ConnectionContextType {
  status: ConnectionStatusType;
  error: string | null;
  setUnauthorizedFromApi: (message: string) => void;
  setOfflineStatusFromProvider: (isOffline: boolean) => void;
  refreshApiClient: (newApiKey?: string | null) => void;
}

export function useAgent(
  agentId: UUID | undefined | null, 
  options = {}
): UseQueryResult<{ data: AgentWithStatus }> {
  // ...
}
```

### Тестирование

**Многоуровневая стратегия тестирования:**

1. **Unit тесты (Bun test)** - Быстрые тесты хуков и утилит
2. **Компонентные тесты (Cypress Component Testing)** - Изолированное тестирование компонентов
3. **E2E тесты (Cypress)** - Сквозное тестирование пользовательских сценариев

**Структура тестов:**

```
src/
├── hooks/__tests__/
│   ├── use-agent-update.test.tsx
│   ├── use-dm-channels.test.ts
│   └── use-sidebar-state.test.ts
├── components/ai-elements/__tests__/
│   └── response.test.tsx
└── lib/
    └── pca.test.ts

cypress/
├── e2e/
│   ├── agent-actions.cy.ts
│   └── chat-flow.cy.ts
└── component/
    └── button.cy.tsx
```

**Пример unit теста:**

```typescript
import { useAgentUpdate } from '../use-agent-update';
import { describe, test, expect, mock, beforeEach } from 'bun:test';
import { renderHook } from '@testing-library/react';

describe('useAgentUpdate hook', () => {
  test('importAgent should call update functions for all fields', () => {
    const initialAgent = { name: 'Initial', /* ... */ };
    const templateAgent = { name: 'Template', /* ... */ };

    const { result } = renderHook(() => useAgentUpdate(initialAgent));
    result.current.importAgent(templateAgent);

    expect(updateField).toHaveBeenCalledWith('name', templateAgent.name);
    expect(updateSettings).toHaveBeenCalledWith(
      expect.objectContaining({ ...templateAgent.settings })
    );
  });
});
```

**Coverage стратегия:**

```json
{
  "test": "bun test",
  "test:unit": "./scripts/run-bun-tests.sh --coverage",
  "test:e2e": "cypress run --e2e",
  "test:all": "./scripts/test-all.sh"
}
```

**Качество тестов:** Средний-высокий уровень. Unit тесты покрывают критичные хуки и утилиты, E2E тесты обеспечивают проверку основных пользовательских сценариев.

### Документация в коде

**JSDoc комментарии** для публичных API:

```typescript
/**
 * Fetches a list of agents from the server with polling.
 *
 * @param options - Optional configuration to override default query behavior.
 * @returns A React Query object containing the agents data and query state.
 *
 * @remark Polling frequency adapts to network conditions.
 */
export function useAgents(options = {}) {
  // ...
}
```

**Inline комментарии** для сложной логики:

```typescript
// Wait for connection if needed
if (!this.isConnected) {
  await this.connectPromise;
}

// CRITICAL: Ensure this loop remains commented out.
// Auto-rejoin on reconnect can cause race conditions.
```

**README.md** в пакете:
- Подробное описание проекта (9810 байт)
- Инструкции по запуску
- Примеры использования

### Соглашения по коду

**Naming conventions:**
- Components: PascalCase (e.g., `ChatComponent`, `AgentCard`)
- Hooks: camelCase с префиксом `use` (e.g., `useAgent`, `useChannelMessages`)
- Files: kebab-case для утилит, PascalCase для компонентов
- Types/Interfaces: PascalCase (e.g., `AgentWithStatus`, `ConnectionContextType`)

**Import организация:**
- External libraries first
- Internal imports с алиасами (@/)
- Types/Interfaces
- Styles

---

## 🔧 Ключевые компоненты

### 1. ConnectionProvider - Управление WebSocket соединением

**Назначение:** Глобальный провайдер для управления состоянием WebSocket соединения, обработки переподключений и авторизации.

**Основные возможности:**
- Отслеживание статуса соединения (loading, connected, reconnecting, error, unauthorized)
- Интеграция с SocketIOManager
- Toast уведомления о событиях соединения
- Обновление API клиента при смене ключа

**Пример использования:**

```typescript
// App.tsx
function App() {
  return (
    <BrowserRouter>
      <AuthProvider>
        <ConnectionProvider>
          <AppContent />
        </ConnectionProvider>
      </AuthProvider>
    </BrowserRouter>
  );
}

// В компоненте
function MyComponent() {
  const { status, error, refreshApiClient } = useConnection();
  
  if (status === 'error') {
    return <Alert>{error}</Alert>;
  }
  
  return <div>Connected</div>;
}
```

**Зависимости:**
- Socket.IO Client (через SocketIOManager)
- Radix Toast (для уведомлений)
- createElizaClient (API клиент)

### 2. useQueryHooks - Централизованное управление серверным состоянием

**Назначение:** Набор кастомных хуков для работы с API через TanStack Query с умными стратегиями кеширования и polling.

**Основные хуки:**
- `useAgents()` - Список всех агентов
- `useAgent(agentId)` - Детали конкретного агента
- `useChannelMessages(channelId)` - Сообщения канала с пагинацией
- `useStartAgent()` / `useStopAgent()` - Управление агентами
- `useAgentRuns(agentId)` - История запусков агента

**Пример использования:**

```typescript
// routes/chat.tsx
export default function AgentRoute() {
  const { agentId } = useParams();
  const { data, isLoading } = useAgent(agentId);
  const { startAgent, isAgentStarting } = useAgentManagement();

  if (isLoading) return <Loader2 className="animate-spin" />;
  
  return (
    <div>
      <h1>{data?.data?.name}</h1>
      <Button onClick={() => startAgent(data.data)}>
        Start Agent
      </Button>
    </div>
  );
}
```

**Зависимости:**
- @tanstack/react-query
- @elizaos/api-client
- lib/api-type-mappers (маппинг типов)

**Особенности:**
- Адаптация частоты polling к качеству сети
- Optimistic updates для мутаций
- Автоматический retry с экспоненциальной задержкой

### 3. SocketIOManager - Real-time коммуникация

**Назначение:** Singleton менеджер для управления WebSocket соединением с использованием Socket.IO и библиотеки Evt для событий.

**Основные методы:**
- `initialize(clientEntityId)` - Инициализация соединения
- `joinChannel(channelId)` - Подписка на канал
- `sendMessage(channelId, content)` - Отправка сообщения
- `on(event, handler)` - Подписка на события
- `emit(event, data)` - Генерация событий

**Типы событий:**
- `messageBroadcast` - Новое сообщение
- `messageComplete` - Завершение генерации
- `controlMessage` - Управляющие команды
- `messageDeleted` / `channelCleared` - Удаление
- `logStream` - Потоковые логи

**Пример использования:**

```typescript
// context/ConnectionContext.tsx
const socketManager = SocketIOManager.getInstance();

useEffect(() => {
  const onConnect = () => setStatus('connected');
  const onDisconnect = () => setStatus('error');

  socketManager.on('connect', onConnect);
  socketManager.on('disconnect', onDisconnect);
  socketManager.on('messageBroadcast', handleNewMessage);

  return () => {
    socketManager.off('connect', onConnect);
    socketManager.off('disconnect', onDisconnect);
  };
}, []);
```

**Зависимости:**
- socket.io-client 4.8.1
- evt 2.5.9 (EventTarget альтернатива)

### 4. ChatComponent - Основной компонент чата

**Назначение:** Комплексный компонент для отображения и взаимодействия с чатом, включающий историю сообщений, ввод, WebSocket интеграцию.

**Основные возможности:**
- Отображение истории сообщений с пагинацией
- Real-time обновления через WebSocket
- Поддержка текстовых и голосовых сообщений
- Markdown рендеринг
- Индикаторы "typing"
- Управление состоянием отправки

**Пример использования:**

```typescript
// routes/chat.tsx
return (
  <ChatComponent
    key={`${agentId}-${channelId}`}
    chatType={ChannelType.DM}
    contextId={agentId}
    serverId={serverId}
    initialDmChannelId={channelId}
  />
);
```

**Основные props:**
- `chatType`: ChannelType (DM / GROUP)
- `contextId`: UUID агента или группы
- `serverId`: UUID сервера
- `initialDmChannelId`: Начальный канал

**Зависимости:**
- useChannelMessages (история сообщений)
- SocketIOManager (real-time)
- ChatInputArea (компонент ввода)
- ChatMessageListComponent (список сообщений)

### 5. Button - Переиспользуемый UI компонент

**Назначение:** Базовый компонент кнопки с вариантами стилей и поддержкой Radix Slot для композиции.

**Варианты:**
- `default` - Основная кнопка
- `destructive` - Деструктивное действие
- `outline` - С обводкой
- `ghost` - Призрачная
- `link` - Ссылка

**Размеры:** default, sm, lg, icon

**Пример использования:**

```typescript
// Обычная кнопка
<Button variant="default" size="lg">
  Click me
</Button>

// Кнопка как ссылка (Slot паттерн)
<Button asChild>
  <Link to="/settings">Settings</Link>
</Button>

// Иконка кнопка
<Button variant="ghost" size="icon">
  <Menu className="h-5 w-5" />
  <span className="sr-only">Menu</span>
</Button>
```

**Зависимости:**
- @radix-ui/react-slot
- class-variance-authority
- tailwind-merge

---

## 📋 Паттерны и best practices

### Переиспользуемые паттерны

**1. Custom Hooks для бизнес-логики**

Вся сложная логика вынесена в кастомные хуки, что обеспечивает переиспользование и тестируемость:

```typescript
// hooks/use-agent-management.ts
export function useAgentManagement() {
  const startAgentMutation = useStartAgent();
  const stopAgentMutation = useStopAgent();
  const [startingAgents, setStartingAgents] = useState<Set<UUID>>(new Set());

  const startAgent = useCallback((agent: Agent) => {
    setStartingAgents(prev => new Set(prev).add(agent.id));
    startAgentMutation.mutate(agent.id, {
      onSettled: () => {
        setStartingAgents(prev => {
          const next = new Set(prev);
          next.delete(agent.id);
          return next;
        });
      },
    });
  }, []);

  return { startAgent, stopAgent, isAgentStarting };
}
```

**2. Singleton паттерн**

Используется для API клиента и SocketIOManager:

```typescript
let elizaClientInstance: ElizaClient | null = null;

export function createElizaClient(): ElizaClient {
  if (!elizaClientInstance) {
    elizaClientInstance = ElizaClient.create(config);
  }
  return elizaClientInstance;
}
```

**3. Compound Components**

Radix UI паттерн для композиции сложных компонентов:

```typescript
<Dialog>
  <DialogTrigger asChild>
    <Button>Open</Button>
  </DialogTrigger>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Title</DialogTitle>
      <DialogDescription>Description</DialogDescription>
    </DialogHeader>
    {/* Content */}
  </DialogContent>
</Dialog>
```

**4. Render Props и Children as Function**

```typescript
<QueryClientProvider client={queryClient}>
  {children}
</QueryClientProvider>
```

### Оптимизации производительности

**1. React Query кеширование**

Агрессивное кеширование с умными стратегиями invalidation:

```typescript
// Prefetch на старте приложения
const prefetchInitialData = async () => {
  await queryClient.prefetchQuery({
    queryKey: ['agents'],
    queryFn: async () => elizaClient.agents.listAgents(),
    staleTime: STALE_TIMES.FREQUENT,
  });
};
```

**2. Code Splitting**

Vite автоматически разделяет код на chunks:

```typescript
// vite.config.ts
output: {
  manualChunks: (id) => {
    if (id.includes('react')) return 'react-vendor';
    if (id.includes('@radix-ui')) return 'ui-vendor';
    if (id.includes('@elizaos')) return 'elizaos-vendor';
  },
}
```

**3. Memo и useMemo**

Используется избирательно для тяжелых вычислений:

```typescript
const transformedMessages = useMemo(
  () => messages.map(transformMessage),
  [messages]
);
```

**4. Virtualization**

Для длинных списков сообщений используется пагинация вместо полной виртуализации:

```typescript
const { 
  fetchNextPage, 
  hasNextPage, 
  isFetchingNextPage 
} = useChannelMessages(channelId);

// Подгрузка при скролле вверх
const handleScroll = () => {
  if (scrollTop < 100 && hasNextPage && !isFetchingNextPage) {
    fetchNextPage();
  }
};
```

### Обработка асинхронных операций

**1. TanStack Query для серверных операций**

Все API-запросы идут через React Query с retry и кешированием.

**2. Async/await с try-catch**

```typescript
const fetchMessages = useCallback(async (beforeTimestamp?: number) => {
  setInternalIsLoading(true);
  try {
    const response = await elizaClient.messaging.getChannelMessages(
      channelId, 
      { limit: 30, before: beforeTimestamp }
    );
    setMessages(response.messages);
  } catch (error) {
    setInternalIsError(true);
    setInternalError(error);
  } finally {
    setInternalIsLoading(false);
  }
}, [channelId]);
```

**3. Promise.all для параллельных запросов**

```typescript
const [agents, servers] = await Promise.all([
  elizaClient.agents.listAgents(),
  elizaClient.servers.listServers(),
]);
```

### Валидация данных

**React Hook Form** с Zod schema validation (через @hookform/resolvers):

```typescript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const agentSchema = z.object({
  name: z.string().min(1, 'Name is required'),
  username: z.string().min(3),
  bio: z.array(z.string()),
});

const { register, handleSubmit, formState: { errors } } = useForm({
  resolver: zodResolver(agentSchema),
});
```

### Локализация

**Статус:** Не реализована полноценная i18n система.

Все строки в коде на английском языке. Для добавления локализации рекомендуется:
- react-i18next или next-intl
- Вынос всех строк в translation файлы
- Поддержка RTL для арабского/иврита

### Безопасность

**1. API Key management**

```typescript
// Хранение в localStorage с namespace
const getLocalStorageApiKey = () => `eliza-api-key-${window.location.origin}`;

export function updateApiClientApiKey(newApiKey: string | null): void {
  if (newApiKey) {
    localStorage.setItem(getLocalStorageApiKey(), newApiKey);
  } else {
    localStorage.removeItem(getLocalStorageApiKey());
  }
  resetElizaClient();
}
```

**2. XSS защита**

- React автоматически экранирует вывод
- Markdown рендеринг через sanitized библиотеки
- CSP headers (настраиваются на сервере)

**3. CORS и Proxy**

Vite proxy для обхода CORS в development:

```typescript
server: {
  proxy: {
    '/api': 'http://localhost:3000',
    '/socket.io': { target: 'http://localhost:3000', ws: true },
  },
}
```

---

## 📊 Инфраструктура разработки

### Скрипты из package.json

**Разработка:**
```bash
bun dev                    # Запуск dev-сервера Vite
bun start:client          # Альтернативная команда запуска
bun dev:client            # Dev-сервер с хостом 0.0.0.0:5173
```

**Сборка:**
```bash
bun build                 # TypeScript компиляция + Vite build
bun preview               # Предпросмотр production сборки
```

**Тестирование:**
```bash
bun test                  # Unit тесты (Bun test)
bun test:all             # Все типы тестов
bun test:unit            # Unit тесты с coverage
bun test:unit:watch      # Unit тесты в watch mode
bun test:component       # Cypress компонентные тесты
bun test:e2e             # Cypress E2E тесты
bun test:e2e:with-server # E2E с запуском сервера
cypress:open             # Cypress UI
bun type-check           # TypeScript проверка типов
```

**Качество кода:**
```bash
bun lint                 # Prettier + ESLint
bun format               # Форматирование кода
bun format:check         # Проверка форматирования
```

**Утилиты:**
```bash
bun clean                # Очистка артефактов сборки
```

### Настройки dev-среды

**Vite Dev Server:**
- Порт: 5173 (strict)
- Hot Module Replacement (HMR)
- Sourcemaps для зависимостей
- File watcher с polling (interval 150ms)
- Ignore patterns для node_modules, .git, .turbo

**TypeScript:**
- Project references (tsconfig.app.json, tsconfig.node.json)
- Path aliases: `@/*` → `./src/*`
- Strict mode enabled

**IDE конфигурация:**
- `.vscode/` с настройками для VSCode
- `.cursor/` для Cursor IDE
- ESLint и Prettier интеграция

### Pre-commit hooks

**Husky 9.1.7** для Git hooks:

```bash
# .husky/pre-commit
bun run pre-commit
```

**Pre-commit скрипт:**
```bash
# scripts/pre-commit-lint.js
bun run scripts/pre-commit-lint.js
```

Проверяет:
- Prettier форматирование
- ESLint правила
- TypeScript компиляция (опционально)

### CI/CD

**GitHub Actions** (`.github/workflows/`):

Конфигурация CI/CD находится в корне монорепозитория. Основные workflow:
- **Test** - Запуск unit и E2E тестов
- **Lint** - Проверка кода
- **Build** - Сборка всех пакетов
- **Deploy** - Деплой (если настроен)

**Типичный workflow:**
```yaml
- name: Setup Bun
  uses: oven-sh/setup-bun@v1
  
- name: Install dependencies
  run: bun install

- name: Run tests
  run: bun test

- name: Build
  run: bun run build
```

### Docker

**Конфигурация:**
- `Dockerfile` в корне монорепозитория
- `docker-compose.yaml` для оркестрации

**Скрипты Docker** (на уровне монорепозитория):
```bash
bun run docker:build    # Сборка образа
bun run docker:run      # Запуск контейнера
bun run docker:start    # Старт сервисов
bun run docker:bash     # Вход в контейнер
```

### Монорепозиторий интеграция

**Turbo** для оркестрации:
```json
// turbo.json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    },
    "test": {
      "dependsOn": ["build"]
    }
  }
}
```

**Workspace зависимости:**
```json
{
  "dependencies": {
    "@elizaos/core": "workspace:*",
    "@elizaos/api-client": "workspace:*"
  }
}
```

### Environment Variables

**Не используются** напрямую в client-пакете. Конфигурация через:
- localStorage для API ключей
- window.location.origin для baseUrl
- Vite define для global переменных

```typescript
// vite.config.ts
define: {
  'process.env': JSON.stringify({}),
  'process.browser': true,
  global: 'globalThis',
}
```

---

## 📝 Выводы и рекомендации

### Сильные стороны

**1. Современный технологический стек**
- React 19, TypeScript 5.9, Vite 7 - новейшие версии с максимальной производительностью
- Активное использование современных возможностей (Suspense готов, Server Components совместимость)

**2. Архитектурная зрелость**
- Четкое разделение ответственности: UI → Hooks → API
- Singleton паттерны для критичных сервисов
- Type-safe API layer через workspace packages

**3. Отличная Developer Experience**
- Bun для быстрой разработки
- HMR с Vite
- Path aliases для удобных импортов
- Comprehensive testing setup

**4. Production-ready код**
- Sourcemaps в продакшене для отладки
- Retry механизмы для API
- Graceful degradation при проблемах с сетью
- Optimistic updates для лучшего UX

**5. Accessibility**
- ESLint a11y плагин
- Radix UI с WAI-ARIA
- Keyboard navigation support

### Области для улучшения

**1. Интернационализация (i18n)**
- **Текущее состояние:** Отсутствует
- **Рекомендация:** Внедрить react-i18next с ленивой загрузкой переводов
- **Приоритет:** Средний

**2. Тестовое покрытие**
- **Текущее состояние:** Частичное покрытие, в основном критичные хуки
- **Рекомендация:** 
  - Увеличить покрытие компонентов до 60-70%
  - Добавить integration тесты для ключевых флоу
  - Настроить coverage gates в CI
- **Приоритет:** Высокий

**3. Performance monitoring**
- **Текущее состояние:** Отсутствует мониторинг метрик
- **Рекомендация:** 
  - Интегрировать Web Vitals (CLS, FID, LCP)
  - Добавить Error Boundary с Sentry интеграцией
  - Performance profiling для тяжелых компонентов
- **Приоритет:** Средний

**4. Code documentation**
- **Текущее состояние:** JSDoc для некоторых функций
- **Рекомендация:**
  - Документировать все публичные API
  - Создать Storybook для UI компонентов
  - Добавить архитектурную документацию (ADR)
- **Приоритет:** Низкий

**5. Bundle optimization**
- **Текущее состояние:** Manual chunk splitting настроен
- **Рекомендация:**
  - Анализ bundle с rollup-plugin-visualizer
  - Lazy loading для тяжелых библиотек (react-force-graph, vis-timeline)
  - Route-based code splitting
- **Приоритет:** Средний

**6. Offline support**
- **Текущее состояние:** Базовая обработка offline состояния
- **Рекомендация:**
  - Service Worker для кеширования ассетов
  - IndexedDB для локального хранения сообщений
  - Offline queue для отложенной отправки
- **Приоритет:** Низкий

### Уровень сложности проекта

**Senior-friendly проект**

**Почему:**
- Сложная архитектура с монорепозиторием
- Real-time коммуникация с WebSocket
- Множество абстракций и паттернов
- Type-safe API интеграция
- Production-grade инфраструктура

**Подходит для:**
- **Senior разработчиков** - Для архитектурных решений и оптимизаций
- **Middle разработчиков** - Для работы с компонентами и бизнес-логикой (с guidance)
- **Junior разработчиков** - Для UI-компонентов и простых фич (с менторингом)

**Требуемые навыки:**
- Глубокое понимание React (Hooks, Context, Performance)
- TypeScript advanced concepts (Generics, Type Guards, Utility Types)
- WebSocket/Real-time коммуникация
- State management patterns (React Query, Context)
- Monorepo tooling (Turbo, workspace dependencies)

### Технический долг

**Минимальный уровень**

Проект демонстрирует высокую культуру кода:
- Актуальные версии зависимостей
- Современные паттерны
- Отсутствие legacy кода
- Регулярный рефакторинг

**Потенциальные точки роста долга:**
- Отсутствие i18n может стать проблемой при глобализации
- Ручной маппинг типов API → Client требует синхронизации

### Рекомендуемые следующие шаги

**Краткосрочные (1-2 месяца):**
1. Увеличить test coverage до 70%
2. Добавить Storybook для UI компонентов
3. Настроить bundle analyzer и оптимизировать размер

**Среднесрочные (3-6 месяцев):**
1. Внедрить i18n систему
2. Добавить Error Boundary с мониторингом
3. Настроить Service Worker для offline-first

**Долгосрочные (6-12 месяцев):**
1. Миграция на React Server Components (если потребуется SSR)
2. Micro-frontend архитектура для масштабирования
3. Advanced performance optimizations (virtualization, memoization)

### Метрики качества

| Метрика | Оценка | Комментарий |
|---------|--------|-------------|
| Архитектура | ⭐⭐⭐⭐⭐ | Отличная, масштабируемая |
| Типизация | ⭐⭐⭐⭐⭐ | Строгая, comprehensive |
| Тестирование | ⭐⭐⭐ | Базовое покрытие |
| Документация | ⭐⭐⭐ | Частичная, нужно улучшить |
| Performance | ⭐⭐⭐⭐ | Хорошая, есть запас для оптимизации |
| DX | ⭐⭐⭐⭐⭐ | Отличная, современные инструменты |
| Accessibility | ⭐⭐⭐⭐ | Хорошая, Radix UI + a11y плагин |
| Security | ⭐⭐⭐⭐ | Базовые практики применены |

### Итоговая оценка

**ElizaOS Client** - это **production-ready**, **современный**, **хорошо структурированный** фронтенд-проект, демонстрирующий высокий уровень инженерной культуры. Проект подходит для масштабирования и дальнейшего развития, имеет минимальный технический долг и использует лучшие практики React-экосистемы 2024-2025 года.

Основные возможности для роста лежат в области тестирования, интернационализации и performance мониторинга. Проект требует опытных разработчиков для поддержки архитектурных решений, но предоставляет отличную основу для онбординга middle-разработчиков.

**Оценка сложности:** 7/10 (Senior-friendly, Middle-accessible с guidance)  
**Качество кода:** 9/10 (Excellent, production-ready)  
**Готовность к масштабированию:** 8/10 (Well-architected, needs some optimizations)

---

_Документ создан: 2024  
Версия проекта: ElizaOS Client v1.6.4-alpha.18  
Автоматический аудит фронтенд кодовой базы_
