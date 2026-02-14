---
id: skill-react-dev
type: skill
name: react-dev
description: This skill should be used when building React components with TypeScript,
  typing hooks, handling events, or when React TypeScript, React 19, Server Components
  are mentioned. Covers type-safe patterns for React 18-19 including generic components,
  proper event typing, and routing integration (TanStack Router, React Router).
category: development
complexity: medium
keywords:
- react
- typescript
capabilities: []
token_estimate: 1493
has_references: true
reference_count: 5
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,493 -->
<!-- Includes Reference Materials -->

> **How to Use This Template**
>
> This template can be used with various AI coding assistants:
> 
> **GitHub Copilot:**
> - Add to `.github/copilot-instructions.md` in your repository
> - Reference in chat: `@workspace` to include in context
> - Add specific sections to your workspace instructions
> 
> **Augment Code:**
> - Load context: `aug context add <path-to-this-file>`
> - Reference in queries naturally
> - Use with specific commands
> 
> **Claude (Desktop/Web):**
> - Add to Project Knowledge in Claude Projects
> - Reference in custom instructions
> - Copy relevant sections as needed
> 
> **ChatGPT:**
> - Add to Custom GPT configuration
> - Include in conversation instructions
> - Use as system prompt
> 
> **Generic Usage:**
> Copy the content below and paste it into your AI assistant's context window
> or system instructions.

---




# react-dev

> This skill should be used when building React components with TypeScript, typing hooks, handling events, or when React TypeScript, React 19, Server Components are mentioned. Covers type-safe patterns for React 18-19 including generic components, proper event typing, and routing integration (TanStack Router, React Router).

# React TypeScript

Type-safe React = compile-time guarantees = confident refactoring.

<when_to_use>

- Building typed React components
- Implementing generic components
- Typing event handlers, forms, refs
- Using React 19 features (Actions, Server Components, use())
- Router integration (TanStack Router, React Router)
- Custom hooks with proper typing

NOT for: non-React TypeScript, vanilla JS React

</when_to_use>

<react_19_changes>

React 19 breaking changes require migration. Key patterns:

**ref as prop** - forwardRef deprecated:

```typescript
// React 19 - ref as regular prop
type ButtonProps = {
  ref?: React.Ref<HTMLButtonElement>;
} & React.ComponentPropsWithoutRef<'button'>;

function Button({ ref, children, ...props }: ButtonProps) {
  return <button ref={ref} {...props}>{children}</button>;
}
```

**useActionState** - replaces useFormState:

```typescript
import { useActionState } from 'react';

type FormState = { errors?: string[]; success?: boolean };

function Form() {
  const [state, formAction, isPending] = useActionState(submitAction, {});
  return <form action={formAction}>...</form>;
}
```

**use()** - unwraps promises/context:

```typescript
function UserProfile({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise); // Suspends until resolved
  return <div>{user.name}</div>;
}
```

See [react-19-patterns.md](references/react-19-patterns.md) for useOptimistic, useTransition, migration checklist.

</react_19_changes>

<component_patterns>

**Props** - extend native elements:

```typescript
type ButtonProps = {
  variant: 'primary' | 'secondary';
} & React.ComponentPropsWithoutRef<'button'>;

function Button({ variant, children, ...props }: ButtonProps) {
  return <button className={variant} {...props}>{children}</button>;
}
```

**Children typing**:

```typescript
type Props = {
  children: React.ReactNode;          // Anything renderable
  icon: React.ReactElement;           // Single element
  render: (data: T) => React.ReactNode;  // Render prop
};
```

**Discriminated unions** for variant props:

```typescript
type ButtonProps =
  | { variant: 'link'; href: string }
  | { variant: 'button'; onClick: () => void };

function Button(props: ButtonProps) {
  if (props.variant === 'link') {
    return <a href={props.href}>Link</a>;
  }
  return <button onClick={props.onClick}>Button</button>;
}
```

</component_patterns>

<event_handlers>

Use specific event types for accurate target typing:

```typescript
// Mouse
function handleClick(e: React.MouseEvent<HTMLButtonElement>) {
  e.currentTarget.disabled = true;
}

// Form
function handleSubmit(e: React.FormEvent<HTMLFormElement>) {
  e.preventDefault();
  const formData = new FormData(e.currentTarget);
}

// Input
function handleChange(e: React.ChangeEvent<HTMLInputElement>) {
  console.log(e.target.value);
}

// Keyboard
function handleKeyDown(e: React.KeyboardEvent<HTMLInputElement>) {
  if (e.key === 'Enter') e.currentTarget.blur();
}
```

See [event-handlers.md](references/event-handlers.md) for focus, drag, clipboard, touch, wheel events.

</event_handlers>

<hooks_typing>

**useState** - explicit for unions/null:

```typescript
const [user, setUser] = useState<User | null>(null);
const [status, setStatus] = useState<'idle' | 'loading'>('idle');
```

**useRef** - null for DOM, value for mutable:

```typescript
const inputRef = useRef<HTMLInputElement>(null);  // DOM - use ?.
const countRef = useRef<number>(0);               // Mutable - direct access
```

**useReducer** - discriminated unions for actions:

```typescript
type Action =
  | { type: 'increment' }
  | { type: 'set'; payload: number };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'set': return { ...state, count: action.payload };
    default: return state;
  }
}
```

**Custom hooks** - tuple returns with as const:

```typescript
function useToggle(initial = false) {
  const [value, setValue] = useState(initial);
  const toggle = () => setValue(v => !v);
  return [value, toggle] as const;
}
```

**useContext** - null guard pattern:

```typescript
const UserContext = createContext<User | null>(null);

function useUser() {
  const user = useContext(UserContext);
  if (!user) throw new Error('useUser outside UserProvider');
  return user;
}
```

See [hooks.md](references/hooks.md) for useCallback, useMemo, useImperativeHandle, useSyncExternalStore.

</hooks_typing>

<generic_components>

Generic components infer types from props - no manual annotations at call site.

**Pattern** - keyof T for column keys, render props for custom rendering:

```typescript
type Column<T> = {
  key: keyof T;
  header: string;
  render?: (value: T[keyof T], item: T) => React.ReactNode;
};

type TableProps<T> = {
  data: T[];
  columns: Column<T>[];
  keyExtractor: (item: T) => string | number;
};

function Table<T>({ data, columns, keyExtractor }: TableProps<T>) {
  return (
    <table>
      <thead>
        <tr>{columns.map(col => <th key={String(col.key)}>{col.header}</th>)}</tr>
      </thead>
      <tbody>
        {data.map(item => (
          <tr key={keyExtractor(item)}>
            {columns.map(col => (
              <td key={String(col.key)}>
                {col.render ? col.render(item[col.key], item) : String(item[col.key])}
              </td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

**Constrained generics** for required properties:

```typescript
type HasId = { id: string | number };

function List<T extends HasId>({ items }: { items: T[] }) {
  return <ul>{items.map(item => <li key={item.id}>...</li>)}</ul>;
}
```

See [generic-components.md](examples/generic-components.md) for Select, List, Modal, FormField patterns.

</generic_components>

<server_components>

React 19 Server Components run on server, can be async.

**Async data fetching**:

```typescript
export default async function UserPage({ params }: { params: { id: string } }) {
  const user = await fetchUser(params.id);
  return <div>{user.name}</div>;
}
```

**Server Actions** - 'use server' for mutations:

```typescript
'use server';

export async function updateUser(userId: string, formData: FormData) {
  await db.user.update({ where: { id: userId }, data: { ... } });
  revalidatePath(`/users/${userId}`);
}
```

**Client + Server Action**:

```typescript
'use client';

import { useActionState } from 'react';
import { updateUser } from '@/actions/user';

function UserForm({ userId }: { userId: string }) {
  const [state, formAction, isPending] = useActionState(
    (prev, formData) => updateUser(userId, formData), {}
  );
  return <form action={formAction}>...</form>;
}
```

**use() for promise handoff**:

```typescript
// Server: pass promise without await
async function Page() {
  const userPromise = fetchUser('123');
  return <UserProfile userPromise={userPromise} />;
}

// Client: unwrap with use()
'use client';
function UserProfile({ userPromise }: { userPromise: Promise<User> }) {
  const user = use(userPromise);
  return <div>{user.name}</div>;
}
```

See [server-components.md](examples/server-components.md) for parallel fetching, streaming, error boundaries.

</server_components>

<routing>

Both TanStack Router and React Router v7 provide type-safe routing solutions.

**TanStack Router** - Compile-time type safety with Zod validation:

```typescript
import { createRoute } from '@tanstack/react-router';
import { z } from 'zod';

const userRoute = createRoute({
  path: '/users/$userId',
  component: UserPage,
  loader: async ({ params }) => ({ user: await fetchUser(params.userId) }),
  validateSearch: z.object({
    tab: z.enum(['profile', 'settings']).optional(),
    page: z.number().int().positive().default(1),
  }),
});

function UserPage() {
  const { user } = useLoaderData({ from: userRoute.id });
  const { tab, page } = useSearch({ from: userRoute.id });
  const { userId } = useParams({ from: userRoute.id });
}
```

**React Router v7** - Automatic type generation with Framework Mode:

```typescript
import type { Route } from "./+types/user";

export async function loader({ params }: Route.LoaderArgs) {
  return { user: await fetchUser(params.userId) };
}

export default function UserPage({ loaderData }: Route.ComponentProps) {
  const { user } = loaderData; // Typed from loader
  return <h1>{user.name}</h1>;
}
```

See [tanstack-router.md](references/tanstack-router.md) for TanStack patterns and [react-router.md](references/react-router.md) for React Router patterns.

</routing>

<rules>

ALWAYS:
- Specific event types (MouseEvent, ChangeEvent, etc)
- Explicit useState for unions/null
- ComponentPropsWithoutRef for native element extension
- Discriminated unions for variant props
- as const for tuple returns
- ref as prop in React 19 (no forwardRef)
- useActionState for form actions
- Type-safe routing patterns (see routing section)

NEVER:
- any for event handlers
- JSX.Element for children (use ReactNode)
- forwardRef in React 19+
- useFormState (deprecated)
- Forget null handling for DOM refs
- Mix Server/Client components in same file
- Await promises when passing to use()

</rules>

<references>

- [hooks.md](references/hooks.md) - useState, useRef, useReducer, useContext, custom hooks
- [event-handlers.md](references/event-handlers.md) - all event types, generic handlers
- [react-19-patterns.md](references/react-19-patterns.md) - useActionState, use(), useOptimistic, migration
- [generic-components.md](examples/generic-components.md) - Table, Select, List, Modal patterns
- [server-components.md](examples/server-components.md) - async components, Server Actions, streaming
- [tanstack-router.md](references/tanstack-router.md) - TanStack Router typed routes, search params, navigation
- [react-router.md](references/react-router.md) - React Router v7 loaders, actions, type generation, forms

</references>


---


## 📚 Reference Materials


### React 19 Patterns

# React 19 TypeScript Patterns

React 19 introduces breaking changes and new APIs requiring updated TypeScript patterns.

## ref as Prop (No More forwardRef)

React 19 allows ref as regular prop — forwardRef deprecated but still works.

```typescript
// ✅ React 19 - ref as prop
type InputProps = {
  ref?: React.Ref<HTMLInputElement>;
  label: string;
} & React.ComponentPropsWithoutRef<'input'>;

export function Input({ ref, label, ...props }: InputProps) {
  return (
    <div>
      <label>{label}</label>
      <input ref={ref} {...props} />
    </div>
  );
}

// Usage
function Form() {
  const inputRef = useRef<HTMLInputElement>(null);

  return (
    <form>
      <Input ref={inputRef} label="Name" />
      <button onClick={() => inputRef.current?.focus()}>Focus</button>
    </form>
  );
}
```

```typescript
// ❌ Old pattern (still works, but unnecessary)
import { forwardRef } from 'react';

type InputProps = {
  label: string;
} & React.ComponentPropsWithoutRef<'input'>;

export const Input = forwardRef<HTMLInputElement, InputProps>(
  ({ label, ...props }, ref) => {
    return (
      <div>
        <label>{label}</label>
        <input ref={ref} {...props} />
      </div>
    );
  }
);

Input.displayName = 'Input';
```

### Generic Components with ref

```typescript
type SelectProps<T> = {
  ref?: React.Ref<HTMLSelectElement>;
  options: T[];
  value: T;
  onChange: (value: T) => void;
  getLabel: (option: T) => string;
};

export function Select<T>({ ref, options, value, onChange, getLabel }: SelectProps<T>) {
  return (
    <select
      ref={ref}
      value={getLabel(value)}
      onChange={(e) => {
        const selected = options.find((opt) => getLabel(opt) === e.target.value);
        if (selected) onChange(selected);
      }}
    >
      {options.map((opt) => (
        <option key={getLabel(opt)} value={getLabel(opt)}>
          {getLabel(opt)}
        </option>
      ))}
    </select>
  );
}
```

### Combining ref with Other Props

```typescript
type ButtonProps = {
  ref?: React.Ref<HTMLButtonElement>;
  variant?: 'primary' | 'secondary';
  size?: 'sm' | 'md' | 'lg';
} & React.ComponentPropsWithoutRef<'button'>;

export function Button({
  ref,
  variant = 'primary',
  size = 'md',
  className,
  children,
  ...props
}: ButtonProps) {
  return (
    <button
      ref={ref}
      className={`btn btn-${variant} btn-${size} ${className || ''}`}
      {...props}
    >
      {children}
    </button>
  );
}
```

## useActionState - Form State Management

Replaces useFormState — manages form submission state with Server Actions.

```typescript
'use client';

import { useActionState } from 'react';

type FormState = {
  success?: boolean;
  errors?: Record<string, string[]>;
  message?: string;
};

type FormData = {
  email: string;
  password: string;
};

// Server Action
async function login(
  prevState: FormState,
  formData: FormData
): Promise<FormState> {
  'use server';

  const email = formData.get('email');
  const password = formData.get('password');

  if (!email || typeof email !== 'string') {
    return {
      success: false,
      errors: { email: ['Email is required'] },
    };
  }

  if (!password || typeof password !== 'string') {
    return {
      success: false,
      errors: { password: ['Password is required'] },
    };
  }

  try {
    await signIn(email, password);
    return { success: true, message: 'Logged in successfully' };
  } catch (error) {
    return {
      success: false,
      message: 'Invalid credentials',
    };
  }
}

// Client Component
export function LoginForm() {
  const [state, formAction, isPending] = useActionState<FormState, FormData>(
    login,
    {} // Initial state
  );

  return (
    <form action={formAction}>
      <div>
        <label htmlFor="email">Email</label>
        <input
          id="email"
          name="email"
          type="email"
          required
          aria-invalid={!!state.errors?.email}
        />
        {state.errors?.email?.map((error) => (
          <p key={error} className="error">{error}</p>
        ))}
      </div>

      <div>
        <label htmlFor="password">Password</label>
        <input
          id="password"
          name="password"
          type="password"
          required
          aria-invalid={!!state.errors?.password}
        />
        {state.errors?.password?.map((error) => (
          <p key={error} className="error">{error}</p>
        ))}
      </div>

      {state.message && (
        <div className={state.success ? 'success' : 'error'}>
          {state.message}
        </div>
      )}

      <button type="submit" disabled={isPending}>
        {isPending ? 'Logging in...' : 'Log In'}
      </button>
    </form>
  );
}
```

### useActionState with Optimistic Updates

```typescript
'use client';

import { useActionState, useOptimistic } from 'react';

type Todo = { id: string; title: string; completed: boolean };

async function toggleTodo(
  prevState: { todos: Todo[] },
  formData: FormData
): Promise<{ todos: Todo[] }> {
  'use server';

  const todoId = formData.get('todoId') as string;
  await db.todo.update({
    where: { id: todoId },
    data: { completed: { toggle: true } },
  });

  const todos = await db.todo.findMany();
  return { todos };
}

export function TodoList({ initialTodos }: { initialTodos: Todo[] }) {
  const [state, formAction] = useActionState(
    toggleTodo,
    { todos: initialTodos }
  );

  const [optimisticTodos, setOptimisticTodos] = useOptimistic(
    state.todos,
    (currentTodos, todoId: string) =>
      currentTodos.map((todo) =>
        todo.id === todoId ? { ...todo, completed: !todo.completed } : todo
      )
  );

  return (
    <ul>
      {optimisticTodos.map((todo) => (
        <li key={todo.id}>
          <form
            action={(formData) => {
              setOptimisticTodos(todo.id);
              formAction(formData);
            }}
          >
            <input type="hidden" name="todoId" value={todo.id} />
            <button type="submit">
              {todo.completed ? '✓' : '○'} {todo.title}
            </button>
          </form>
        </li>
      ))}
    </ul>
  );
}
```

## use() Hook - Unwrapping Resources

use() unwraps promises and context — enables new patterns for data fetching.

### use() with Promises

```typescript
// Server Component
async function UserPage({ params }: { params: { id: string } }) {
  // Pass promise without awaiting
  const userPromise = fetchUser(params.id);

  return (
    <Suspense fallback={<UserSkeleton />}>
      <UserProfile userPromise={userPromise} />
    </Suspense>
  );
}

// Client Component
'use client';

import { use } from 'react';

type UserProfileProps = {
  userPromise: Promise<User>;
};

export function UserProfile({ userPromise }: UserProfileProps) {
  // Suspends until resolved
  const user = use(userPromise);

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

### Conditional use()

use() can be called conditionally — unlike hooks.

```typescript
'use client';

import { use } from 'react';

type Props = {
  userPromise?: Promise<User>;
  userId?: string;
};

export function UserDisplay({ userPromise, userId }: Props) {
  let user: User | undefined;

  if (userPromise) {
    user = use(userPromise); // Conditional use() - allowed!
  } else if (userId) {
    // Fetch inline
    user = use(fetchUser(userId));
  }

  if (!user) return <div>No user data</div>;

  return <div>{user.name}</div>;
}
```

### use() in Loops

```typescript
'use client';

import { use } from 'react';

type Props = {
  userPromises: Promise<User>[];
};

export function UserList({ userPromises }: Props) {
  return (
    <ul>
      {userPromises.map((promise, index) => {
        const user = use(promise); // use() in loop - allowed!
        return <li key={user.id}>{user.name}</li>;
      })}
    </ul>
  );
}
```

### use() with Context

Alternative to useContext — can be called conditionally.

```typescript
import { createContext, use } from 'react';

type Theme = 'light' | 'dark';
const ThemeContext = createContext<Theme>('light');

export function ThemedComponent({ override }: { override?: Theme }) {
  let theme: Theme;

  if (override) {
    theme = override;
  } else {
    theme = use(ThemeContext); // Conditional context access
  }

  return <div className={theme}>Content</div>;
}
```

## useOptimistic - Optimistic UI Updates

Show immediate UI feedback before server confirms.

```typescript
'use client';

import { useOptimistic } from 'react';

type Message = { id: string; text: string; sending?: boolean };

export function MessageThread({ messages }: { messages: Message[] }) {
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (state, newMessage: Message) => [...state, newMessage]
  );

  async function sendMessage(formData: FormData) {
    const text = formData.get('message') as string;

    // Add optimistic message immediately
    addOptimisticMessage({ id: 'temp', text, sending: true });

    // Send to server
    await fetch('/api/messages', {
      method: 'POST',
      body: JSON.stringify({ text }),
    });
  }

  return (
    <div>
      <ul>
        {optimisticMessages.map((msg) => (
          <li key={msg.id} className={msg.sending ? 'opacity-50' : ''}>
            {msg.text}
          </li>
        ))}
      </ul>

      <form action={sendMessage}>
        <input name="message" required />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```

### useOptimistic with State Updates

```typescript
'use client';

import { useOptimistic, useState, useTransition } from 'react';

type Item = { id: string; name: string; quantity: number };

export function ShoppingCart({ items: initialItems }: { items: Item[] }) {
  const [items, setItems] = useState(initialItems);
  const [isPending, startTransition] = useTransition();

  const [optimisticItems, updateOptimistic] = useOptimistic(
    items,
    (state, { id, quantity }: { id: string; quantity: number }) =>
      state.map((item) =>
        item.id === id ? { ...item, quantity } : item
      )
  );

  async function updateQuantity(id: string, quantity: number) {
    // Optimistic update
    updateOptimistic({ id, quantity });

    // Server update
    startTransition(async () => {
      const updated = await fetch(`/api/cart/${id}`, {
        method: 'PATCH',
        body: JSON.stringify({ quantity }),
      }).then((r) => r.json());

      setItems(updated);
    });
  }

  return (
    <ul>
      {optimisticItems.map((item) => (
        <li key={item.id}>
          {item.name}
          <button onClick={() => updateQuantity(item.id, item.quantity - 1)}>
            -
          </button>
          <span>{item.quantity}</span>
          <button onClick={() => updateQuantity(item.id, item.quantity + 1)}>
            +
          </button>
        </li>
      ))}
    </ul>
  );
}
```

## useTransition - Non-Blocking Updates

Mark state updates as non-urgent — UI stays responsive.

```typescript
'use client';

import { useTransition, useState } from 'react';

export function SearchResults() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState<string[]>([]);
  const [isPending, startTransition] = useTransition();

  function handleSearch(value: string) {
    setQuery(value); // Urgent - update input immediately

    startTransition(() => {
      // Non-urgent - can be interrupted
      const filtered = hugeDataset.filter((item) =>
        item.toLowerCase().includes(value.toLowerCase())
      );
      setResults(filtered);
    });
  }

  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={(e) => handleSearch(e.target.value)}
        placeholder="Search..."
      />

      {isPending && <div>Searching...</div>}

      <ul>
        {results.map((result) => (
          <li key={result}>{result}</li>
        ))}
      </ul>
    </div>
  );
}
```

### useTransition with Server Actions

```typescript
'use client';

import { useTransition } from 'react';
import { deletePost } from '@/actions/posts';

export function DeleteButton({ postId }: { postId: string }) {
  const [isPending, startTransition] = useTransition();

  function handleDelete() {
    startTransition(async () => {
      await deletePost(postId);
      // UI stays responsive during deletion
    });
  }

  return (
    <button onClick={handleDelete} disabled={isPending}>
      {isPending ? 'Deleting...' : 'Delete'}
    </button>
  );
}
```

## useDeferredValue - Deferred Rendering

Defer expensive re-renders while keeping UI responsive.

```typescript
'use client';

import { useDeferredValue, useState } from 'react';

export function ProductSearch() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);

  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search products..."
      />

      {/* Uses deferred value - won't block input */}
      <ExpensiveResults query={deferredQuery} />
    </div>
  );
}

function ExpensiveResults({ query }: { query: string }) {
  const results = useMemo(() => {
    // Expensive filtering/sorting
    return products.filter((p) => p.name.includes(query));
  }, [query]);

  return (
    <ul>
      {results.map((result) => (
        <li key={result.id}>{result.name}</li>
      ))}
    </ul>
  );
}
```

## Migration Checklist

Updating from React 18 to React 19:

- [ ] Replace forwardRef with ref as prop
- [ ] Replace useFormState with useActionState
- [ ] Update Server Action types to include prevState parameter
- [ ] Use use() for promises in Server Components
- [ ] Add 'use server' directive to Server Actions
- [ ] Add 'use client' directive to Client Components
- [ ] Update TypeScript to 5.0+ for better React 19 support
- [ ] Update @types/react to 19.x
- [ ] Test all forms with useActionState
- [ ] Verify ref forwarding works without forwardRef




### Hooks

# Hook TypeScript Patterns

Type-safe hook patterns for useState, useRef, useReducer, useContext, and custom hooks.

## useState

Type inference works for simple types; explicit typing needed for unions/null.

```typescript
// Inference works
const [count, setCount] = useState(0); // number
const [name, setName] = useState(''); // string
const [items, setItems] = useState<string[]>([]); // explicit for empty arrays

// Explicit for unions/null
const [user, setUser] = useState<User | null>(null);
const [status, setStatus] = useState<'idle' | 'loading' | 'success'>('idle');

// Complex initial state
type FormData = { name: string; email: string };
const [formData, setFormData] = useState<FormData>({
  name: '',
  email: '',
});

// Lazy initialization
const [data, setData] = useState<Data>(() => {
  const cached = localStorage.getItem('data');
  return cached ? JSON.parse(cached) : defaultData;
});
```

## useRef

Distinguish DOM refs (null initial) from mutable value refs (value initial).

```typescript
// DOM element ref - null initial, readonly .current
const inputRef = useRef<HTMLInputElement>(null);
const buttonRef = useRef<HTMLButtonElement>(null);
const divRef = useRef<HTMLDivElement>(null);

useEffect(() => {
  inputRef.current?.focus(); // Optional chaining for null
}, []);

// Mutable value ref - non-null initial, mutable .current
const countRef = useRef<number>(0);
countRef.current += 1; // No optional chaining

const previousValueRef = useRef<string | undefined>(undefined);
previousValueRef.current = currentValue;

// Interval/timeout ref
const timeoutRef = useRef<ReturnType<typeof setTimeout>>();
const intervalRef = useRef<ReturnType<typeof setInterval>>();

useEffect(() => {
  timeoutRef.current = setTimeout(() => {}, 1000);
  return () => {
    if (timeoutRef.current) clearTimeout(timeoutRef.current);
  };
}, []);

// Callback ref for dynamic elements
const callbackRef = useCallback((node: HTMLDivElement | null) => {
  if (node) {
    node.scrollIntoView({ behavior: 'smooth' });
  }
}, []);
```

## useReducer

Typed actions with discriminated unions.

```typescript
type State = {
  count: number;
  status: 'idle' | 'loading' | 'success' | 'error';
  error?: string;
};

type Action =
  | { type: 'increment' }
  | { type: 'decrement' }
  | { type: 'set'; payload: number }
  | { type: 'setStatus'; payload: State['status'] }
  | { type: 'setError'; payload: string };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'increment':
      return { ...state, count: state.count + 1 };
    case 'decrement':
      return { ...state, count: state.count - 1 };
    case 'set':
      return { ...state, count: action.payload };
    case 'setStatus':
      return { ...state, status: action.payload };
    case 'setError':
      return { ...state, status: 'error', error: action.payload };
    default:
      return state;
  }
}

function Component() {
  const [state, dispatch] = useReducer(reducer, {
    count: 0,
    status: 'idle',
  });

  dispatch({ type: 'set', payload: 10 }); // Type-safe
  dispatch({ type: 'set' }); // Error: payload required
  dispatch({ type: 'unknown' }); // Error: invalid action type
}
```

### Reducer with Context

```typescript
type AuthState = {
  user: User | null;
  isAuthenticated: boolean;
};

type AuthAction =
  | { type: 'login'; payload: User }
  | { type: 'logout' };

type AuthContextValue = {
  state: AuthState;
  dispatch: React.Dispatch<AuthAction>;
};

const AuthContext = createContext<AuthContextValue | null>(null);

function authReducer(state: AuthState, action: AuthAction): AuthState {
  switch (action.type) {
    case 'login':
      return { user: action.payload, isAuthenticated: true };
    case 'logout':
      return { user: null, isAuthenticated: false };
  }
}

function AuthProvider({ children }: { children: React.ReactNode }) {
  const [state, dispatch] = useReducer(authReducer, {
    user: null,
    isAuthenticated: false,
  });

  return (
    <AuthContext.Provider value={{ state, dispatch }}>
      {children}
    </AuthContext.Provider>
  );
}

function useAuth() {
  const context = useContext(AuthContext);
  if (!context) throw new Error('useAuth must be used within AuthProvider');
  return context;
}
```

## useContext

Typed context with and without default values.

```typescript
// Context with default value
type Theme = 'light' | 'dark';
const ThemeContext = createContext<Theme>('light');

function useTheme() {
  return useContext(ThemeContext); // Always Theme, never null
}

// Context without default (must handle null)
type User = { id: string; name: string };
const UserContext = createContext<User | null>(null);

function useUser() {
  const user = useContext(UserContext);
  if (!user) throw new Error('useUser must be used within UserProvider');
  return user; // Type narrowed to User
}

// Context with complex value
type AppContextValue = {
  theme: Theme;
  user: User | null;
  setTheme: (theme: Theme) => void;
  login: (user: User) => void;
  logout: () => void;
};

const AppContext = createContext<AppContextValue | null>(null);

function useApp() {
  const context = useContext(AppContext);
  if (!context) throw new Error('useApp must be used within AppProvider');
  return context;
}
```

## Custom Hooks

Return type patterns for simple and complex hooks.

```typescript
// Object return - properties accessed by name
function useCounter(initial: number) {
  const [count, setCount] = useState(initial);
  const increment = () => setCount((c) => c + 1);
  const decrement = () => setCount((c) => c - 1);
  const reset = () => setCount(initial);

  return { count, increment, decrement, reset };
}

// Usage
const { count, increment } = useCounter(0);

// Tuple return - positional destructuring
function useToggle(initial = false): [boolean, () => void, () => void, () => void] {
  const [value, setValue] = useState(initial);
  const toggle = () => setValue((v) => !v);
  const setTrue = () => setValue(true);
  const setFalse = () => setValue(false);

  return [value, toggle, setTrue, setFalse];
}

// Usage
const [isOpen, toggleOpen, open, close] = useToggle();

// as const for tuple inference
function useLocalStorage<T>(key: string, initial: T) {
  const [value, setValue] = useState<T>(() => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initial;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue] as const; // readonly tuple
}

// Generic custom hook
function useFetch<T>(url: string) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    let cancelled = false;

    fetch(url)
      .then((res) => res.json())
      .then((json: T) => {
        if (!cancelled) {
          setData(json);
          setLoading(false);
        }
      })
      .catch((err) => {
        if (!cancelled) {
          setError(err);
          setLoading(false);
        }
      });

    return () => {
      cancelled = true;
    };
  }, [url]);

  return { data, loading, error };
}

// Usage - T inferred from usage or explicit
const { data } = useFetch<User[]>('/api/users');
```

## useCallback and useMemo

Typed callbacks and memoized values.

```typescript
// useCallback with typed parameters
const handleClick = useCallback((id: string, event: React.MouseEvent) => {
  console.log(id, event.target);
}, []);

// useCallback returning value
const formatDate = useCallback((date: Date): string => {
  return date.toLocaleDateString();
}, []);

// useMemo with explicit return type
const sortedItems = useMemo((): Item[] => {
  return [...items].sort((a, b) => a.name.localeCompare(b.name));
}, [items]);

// useMemo with complex computation
const stats = useMemo(() => {
  return {
    total: items.length,
    average: items.reduce((a, b) => a + b.value, 0) / items.length,
    max: Math.max(...items.map((i) => i.value)),
  };
}, [items]);
```

## useImperativeHandle

Expose imperative methods from components.

```typescript
type InputHandle = {
  focus: () => void;
  clear: () => void;
  getValue: () => string;
};

type InputProps = {
  ref?: React.Ref<InputHandle>;
  label: string;
};

function CustomInput({ ref, label }: InputProps) {
  const inputRef = useRef<HTMLInputElement>(null);

  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current?.focus(),
    clear: () => {
      if (inputRef.current) inputRef.current.value = '';
    },
    getValue: () => inputRef.current?.value ?? '',
  }));

  return (
    <div>
      <label>{label}</label>
      <input ref={inputRef} />
    </div>
  );
}

// Usage
function Form() {
  const inputRef = useRef<InputHandle>(null);

  const handleSubmit = () => {
    const value = inputRef.current?.getValue();
    inputRef.current?.clear();
  };

  return (
    <form>
      <CustomInput ref={inputRef} label="Name" />
      <button onClick={() => inputRef.current?.focus()}>Focus</button>
    </form>
  );
}
```

## useLayoutEffect

Same signature as useEffect, runs synchronously after DOM mutations.

```typescript
function Tooltip({ targetRef }: { targetRef: React.RefObject<HTMLElement> }) {
  const tooltipRef = useRef<HTMLDivElement>(null);
  const [position, setPosition] = useState({ top: 0, left: 0 });

  useLayoutEffect(() => {
    if (targetRef.current && tooltipRef.current) {
      const rect = targetRef.current.getBoundingClientRect();
      setPosition({
        top: rect.bottom + 8,
        left: rect.left,
      });
    }
  }, [targetRef]);

  return (
    <div
      ref={tooltipRef}
      style={{ position: 'fixed', top: position.top, left: position.left }}
    >
      Tooltip content
    </div>
  );
}
```

## useId

Generate unique IDs for accessibility.

```typescript
function FormField({ label }: { label: string }) {
  const id = useId();
  const errorId = useId();

  return (
    <div>
      <label htmlFor={id}>{label}</label>
      <input id={id} aria-describedby={errorId} />
      <span id={errorId}>Error message</span>
    </div>
  );
}
```

## useSyncExternalStore

Subscribe to external stores with SSR support.

```typescript
type Store<T> = {
  getState: () => T;
  subscribe: (callback: () => void) => () => void;
};

function useStore<T>(store: Store<T>): T {
  return useSyncExternalStore(
    store.subscribe,
    store.getState,
    store.getState // Server snapshot
  );
}

// Example: window width store
const widthStore: Store<number> = {
  getState: () => (typeof window !== 'undefined' ? window.innerWidth : 0),
  subscribe: (callback) => {
    window.addEventListener('resize', callback);
    return () => window.removeEventListener('resize', callback);
  },
};

function useWindowWidth() {
  return useSyncExternalStore(
    widthStore.subscribe,
    widthStore.getState,
    () => 0 // Server fallback
  );
}
```




### React Router

# React Router v7 - Comprehensive Reference Guide

## Overview

React Router is a modern routing library that provides flexible client-side and server-side routing for React applications. It offers two primary modes of operation: Framework Mode and Data Mode, with first-class TypeScript support through automatic type generation.

## Installation and Setup

### Basic Installation

```bash
npm install react-router
```

### TypeScript Configuration

React Router generates type-specific files for each route. Configure your `tsconfig.json`:

```json
{
  "include": [".react-router/types/**/*"],
  "compilerOptions": {
    "rootDirs": [".", "./.react-router/types"]
  }
}
```

Add `.react-router/` to `.gitignore`:

```txt
.react-router/
```

### Extending AppLoadContext

Define your app's context type in a `.ts` or `.d.ts` file:

```typescript
import "react-router";

declare module "react-router" {
  interface AppLoadContext {
    // add context properties here
    apiClient?: APIClient;
    userId?: string;
  }
}
```

## Route Definition Patterns

### Basic Route Configuration

Routes are defined as objects passed to `createBrowserRouter`:

```tsx
import { createBrowserRouter } from "react-router";

const router = createBrowserRouter([
  {
    path: "/",
    Component: Root
  },
  {
    path: "/about",
    Component: About
  },
]);
```

### Nested Routes

Child routes are rendered through an `<Outlet/>` in the parent component:

```tsx
createBrowserRouter([
  {
    path: "/dashboard",
    Component: Dashboard,
    children: [
      { index: true, Component: DashboardHome },
      { path: "settings", Component: Settings },
    ],
  },
]);

function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Outlet /> {/* Renders child route */}
    </div>
  );
}
```

### Layout Routes (No Path)

Create layout containers without adding URL segments:

```tsx
createBrowserRouter([
  {
    // no path - just a layout
    Component: MarketingLayout,
    children: [
      { index: true, Component: Home },
      { path: "contact", Component: Contact },
    ],
  },
]);
```

### Index Routes

Define default routes at a path:

```tsx
createBrowserRouter([
  {
    path: "/",
    Component: Root,
    children: [
      { index: true, Component: Home }, // renders at "/"
      { path: "about", Component: About },
    ],
  },
]);
```

### Dynamic Segments

Use colons to define dynamic parameters:

```tsx
{
  path: "teams/:teamId",
  loader: async ({ params }) => {
    return await fetchTeam(params.teamId);
  },
  Component: Team,
}
```

Multiple dynamic segments:

```tsx
{
  path: "posts/:postId/comments/:commentId",
  // params.postId and params.commentId available
}
```

### Optional Segments

```tsx
{
  path: ":lang?/categories" // lang is optional
}
```

### Splat Routes (Catchall)

```tsx
{
  path: "files/*",
  loader: async ({ params }) => {
    const splat = params["*"]; // remaining URL after "files/"
  },
}
```

## TypeScript Typing

### Route Type Imports

In Framework Mode, import route-specific types from the generated `+types` directory:

```tsx
import type { Route } from "./+types/product";

export async function loader({ params }: Route.LoaderArgs) {
  // params is typed as { id: string }
  return { planet: `world #${params.id}` };
}

export default function Product({
  loaderData, // typed from loader
}: Route.ComponentProps) {
  return <h1>{loaderData.planet}</h1>;
}
```

### Generated Types

React Router generates these types for each route:

- `Route.LoaderArgs` - Loader function arguments
- `Route.ClientLoaderArgs` - Client loader arguments
- `Route.ActionArgs` - Action function arguments
- `Route.ClientActionArgs` - Client action arguments
- `Route.ComponentProps` - Props for default export
- `Route.ErrorBoundaryProps` - Props for ErrorBoundary
- `Route.HydrateFallbackProps` - Props for HydrateFallback

### Manual Loader Data Typing

In Data Mode, type loader data manually:

```tsx
export async function loader() {
  return { invoices: await getInvoices() };
}

export default function Invoices() {
  const data = useLoaderData<typeof loader>();
  // data is typed as { invoices: ... }
}
```

### Action Data Typing

```tsx
export async function action({ request }: Route.ActionArgs) {
  const formData = await request.formData();
  return { message: `Hello, ${formData.get("name")}` };
}

export default function Form() {
  const actionData = useActionData<typeof action>();
  // actionData is typed correctly
}
```

## Data Loading with Loaders

### Basic Loader Pattern

Loaders provide data to route components before rendering:

```tsx
createBrowserRouter([
  {
    path: "/posts/:postId",
    loader: async ({ params }) => {
      const post = await fetchPost(params.postId);
      return { post };
    },
    Component: Post,
  },
]);

function Post() {
  const { post } = useLoaderData();
  return <h1>{post.title}</h1>;
}
```

### Loader Arguments

```tsx
type LoaderFunctionArgs = {
  params: Params; // URL parameters
  request: Request; // fetch API Request
  context?: AppLoadContext; // shared context
};
```

### Error Handling in Loaders

Throw errors with `data()` function for client-side error boundaries:

```tsx
import { data } from "react-router";

export async function loader({ params }: Route.LoaderArgs) {
  const record = await fakeDb.getRecord(params.id);

  if (!record) {
    throw data("Record Not Found", { status: 404 });
  }

  return record;
}
```

### Revalidation Control

Control when loaders rerun:

```tsx
export const shouldRevalidate: ShouldRevalidateFunctionArgs = (args) => {
  // Return true to revalidate, false to skip
  // Default: true when params change or after successful action
  return true;
};
```

### Lazy Loading

Load components and loaders on-demand:

```tsx
{
  path: "/app",
  lazy: async () => {
    const { Component, loader } = await import("./app");
    return { Component, loader };
  },
}
```

## Actions and Form Handling

### Basic Action Pattern

Actions handle form submissions and mutations:

```tsx
createBrowserRouter([
  {
    path: "/projects",
    action: async ({ request }) => {
      const formData = await request.formData();
      const title = formData.get("title");
      const project = await createProject({ title });
      return project; // data available via useActionData
    },
    Component: Projects,
  },
]);
```

### Form Submission Methods

#### Using `<Form>` Component

Causes navigation and adds history entry:

```tsx
import { Form } from "react-router";

export default function CreateEvent() {
  return (
    <Form action="/events" method="post">
      <input type="text" name="title" />
      <button type="submit">Create</button>
    </Form>
  );
}
```

#### Using `useSubmit` Hook

Imperative form submission:

```tsx
import { useSubmit } from "react-router";

export default function Timer() {
  const submit = useSubmit();

  const handleTimeout = () => {
    submit(
      { quizTimedOut: true },
      { action: "/end-quiz", method: "post" }
    );
  };

  return <div>{/* ... */}</div>;
}
```

#### Using `useFetcher` Hook

Submit without navigation (no history entry):

```tsx
import { useFetcher } from "react-router";

export default function Task() {
  const fetcher = useFetcher();

  return (
    <fetcher.Form method="post" action="/update-task/123">
      <input type="text" name="title" />
      <button type="submit">Save</button>
    </fetcher.Form>
  );
}
```

### Form Validation

Return errors with non-2xx status codes:

```tsx
export async function action({ request }: Route.ActionArgs) {
  const formData = await request.formData();
  const email = String(formData.get("email"));

  const errors: Record<string, string> = {};

  if (!email.includes("@")) {
    errors.email = "Invalid email";
  }

  if (Object.keys(errors).length > 0) {
    return data({ errors }, { status: 400 }); // Don't revalidate
  }

  await createUser({ email });
  return redirect("/dashboard");
}

export default function Signup() {
  const fetcher = useFetcher();
  const errors = fetcher.data?.errors;

  return (
    <fetcher.Form method="post">
      <input name="email" />
      {errors?.email && <span>{errors.email}</span>}
      <button>Sign Up</button>
    </fetcher.Form>
  );
}
```

### Accessing Action Data

```tsx
import { useActionData } from "react-router";

export default function Project() {
  const actionData = useActionData<typeof action>();

  return (
    <div>
      <Form method="post">
        {/* form fields */}
      </Form>
      {actionData && <p>Success: {actionData.message}</p>}
    </div>
  );
}
```

## Navigation Patterns

### Link Component

For client-side navigation without active styling:

```tsx
import { Link } from "react-router";

<Link to="/dashboard">Dashboard</Link>

<Link to={{
  pathname: "/search",
  search: "?q=react",
  hash: "#results"
}}>
  Search
</Link>
```

### NavLink Component

For navigation with active/pending states:

```tsx
import { NavLink } from "react-router";

<NavLink to="/messages">
  {({ isActive, isPending }) => (
    <span className={isActive ? "active" : ""}>
      Messages
    </span>
  )}
</NavLink>
```

### Programmatic Navigation

```tsx
import { useNavigate } from "react-router";

export default function LogoutButton() {
  const navigate = useNavigate();

  return (
    <button onClick={() => navigate("/")}>
      Go Home
    </button>
  );
}
```

### Navigate Options

```tsx
navigate("/path", {
  replace: true,              // Don't add to history
  state: { from: "/" },       // Pass state
  relative: "route",          // Or "path"
  preventScrollReset: true,   // Don't scroll to top
  viewTransition: true,       // Enable view transition
});

navigate(-1);  // Go back
navigate(1);   // Go forward
```

### Redirect in Loaders/Actions

```tsx
import { redirect } from "react-router";

export async function loader({ request }: Route.LoaderArgs) {
  const user = await getUser(request);
  if (!user) {
    return redirect("/login");
  }
  return { user };
}

export async function action() {
  const project = await createProject();
  return redirect(`/projects/${project.id}`);
}
```

## URL Parameters and Search Params

### Route Parameters

Access dynamic route parameters:

```tsx
import { useParams } from "react-router";

export default function Post() {
  const params = useParams<{ postId: string }>();
  // params.postId is typed as string

  return <h1>Post: {params.postId}</h1>;
}
```

### Search Parameters

Handle query strings:

```tsx
import { useSearchParams } from "react-router";

export default function Search() {
  const [searchParams, setSearchParams] = useSearchParams();

  const query = searchParams.get("q");
  const page = searchParams.get("page") || "1";

  return (
    <div>
      <input value={query} />
      <button onClick={() => setSearchParams({ q: "react" })}>
        Search
      </button>
    </div>
  );
}
```

### Setting Search Params

```tsx
// String
setSearchParams("?tab=1");

// Object
setSearchParams({ tab: "1" });

// Multiple values
setSearchParams({ brand: ["nike", "reebok"] });

// Callback
setSearchParams((prev) => {
  prev.set("tab", "2");
  return prev;
});
```

## Error Handling

### Framework Mode Error Boundaries

```tsx
import { Route } from "./+types/root";
import { isRouteErrorResponse } from "react-router";

export function ErrorBoundary({ error }: Route.ErrorBoundaryProps) {
  if (isRouteErrorResponse(error)) {
    return (
      <div>
        <h1>{error.status} {error.statusText}</h1>
        <p>{error.data}</p>
      </div>
    );
  }

  if (error instanceof Error) {
    return (
      <div>
        <h1>Error</h1>
        <p>{error.message}</p>
      </div>
    );
  }

  return <h1>Unknown Error</h1>;
}
```

### Data Mode Error Boundaries

```tsx
import { useRouteError, isRouteErrorResponse } from "react-router";

function RootErrorBoundary() {
  const error = useRouteError();

  if (isRouteErrorResponse(error)) {
    return (
      <div>
        <h1>{error.status}</h1>
        <p>{error.data}</p>
      </div>
    );
  }

  return <h1>Error</h1>;
}

createBrowserRouter([
  {
    path: "/",
    Component: Root,
    ErrorBoundary: RootErrorBoundary,
  },
]);
```

### Throwing Errors

Unintentional errors are caught:

```tsx
export async function loader() {
  return undefined(); // Error thrown and caught
}
```

Intentional errors with status codes:

```tsx
import { data } from "react-router";

export async function loader({ params }: Route.LoaderArgs) {
  const post = await getPost(params.id);
  if (!post) {
    throw data("Not Found", { status: 404 });
  }
  return post;
}
```

## Advanced Hooks

### useLoaderData

Get data from the route's loader:

```tsx
export default function Component() {
  const data = useLoaderData<typeof loader>();
  // fully typed
}
```

### useActionData

Get data from the last action submission:

```tsx
export default function Form() {
  const actionData = useActionData<typeof action>();
  return actionData ? <p>{actionData.message}</p> : null;
}
```

### useFetcher

Independent data submission without navigation:

```tsx
const fetcher = useFetcher();

fetcher.state; // "idle" | "loading" | "submitting"
fetcher.data; // data from action/loader
fetcher.Form; // component for forms
fetcher.load(path); // load data
fetcher.submit(data, options); // submit data
fetcher.reset(); // reset state
```

Keyed fetchers (access from other components):

```tsx
const fetcher = useFetcher({ key: "my-fetcher" });
```

### useMatches

Get all active route matches:

```tsx
const matches = useMatches();
// Array of: { route, pathname, params, data, handle }

matches.forEach(match => {
  console.log(match.data);    // Loader data
  console.log(match.handle);  // Route handle metadata
});
```

### useLocation

Get current location:

```tsx
const location = useLocation();
// {
//   pathname: "/posts/123",
//   search: "?tab=1",
//   hash: "#section",
//   state: { from: "/" }
// }
```

### useNavigation

Track navigation state:

```tsx
const navigation = useNavigation();

navigation.state; // "idle" | "loading" | "submitting"
navigation.location; // new location during navigation
navigation.formData; // form data being submitted
```

## Advanced Route Features

### Route Handle Property

Attach metadata to routes for use in ancestor components:

```tsx
// Route definition
export const handle = {
  breadcrumb: () => <Link to="/posts">Posts</Link>,
  icon: "📝",
};

// Access in ancestor
function Root() {
  const matches = useMatches();

  return (
    <header>
      {matches
        .filter(m => m.handle?.breadcrumb)
        .map(m => m.handle.breadcrumb())}
    </header>
  );
}
```

### Middleware

Run code before/after navigations:

```tsx
export async function middleware({ request }, next) {
  console.log(`Starting: ${request.url}`);
  await next();
  console.log("Complete");
}

createBrowserRouter([
  {
    path: "/",
    middleware: [middleware],
    loader: rootLoader,
    Component: Root,
  },
]);
```

### Outlet Component

Render child routes:

```tsx
import { Outlet } from "react-router";

function Layout() {
  return (
    <div>
      <nav>{/* navigation */}</nav>
      <Outlet /> {/* Child routes render here */}
    </div>
  );
}
```

### OutletContext

Pass data to child routes via Outlet:

```tsx
// Parent
function Parent() {
  return <Outlet context={{ user: { name: "John" } }} />;
}

// Child
import { useOutletContext } from "react-router";

function Child() {
  const { user } = useOutletContext<{ user: User }>();
  return <p>{user.name}</p>;
}
```

## Comparison with TanStack Router

### Type Safety Similarities

Both React Router v7 and TanStack Router offer first-class TypeScript support:

| Feature | React Router v7 | TanStack Router |
|---------|-----------------|-----------------|
| Route type generation | ✓ Automatic `.react-router/types/` | ✓ Automatic |
| Param typing | ✓ Auto-inferred from path | ✓ Auto-inferred |
| Loader data typing | ✓ Via `Route.LoaderArgs` | ✓ Via `loader()` return type |
| Action typing | ✓ Via `Route.ActionArgs` | ✓ Via `action()` return type |
| Search params typing | ✓ Manual with `useSearchParams` | ✓ Route-level definition |
| Redirect typing | ✓ Standard function | ✓ Standard function |

### Key Differences

**Type Definition Level:**
- React Router: File-based type generation (`.d.ts` files)
- TanStack Router: Route-level type registration

**Search Params:**
- React Router: Runtime `URLSearchParams` API
- TanStack Router: Compile-time schema validation

**Nested Route Typing:**
- React Router: Inherited from parent context
- TanStack Router: Explicit inheritance patterns

**Error Typing:**
- React Router: `isRouteErrorResponse()` utility
- TanStack Router: Built-in discriminated unions

**Learning Curve:**
- React Router: Familiar to those migrating from v6
- TanStack Router: More opinionated, explicit type patterns

## Best Practices

### 1. Type Your Routes Consistently

```tsx
// In Framework Mode, always use the generated types
import type { Route } from "./+types/my-route";

export async function loader({ params }: Route.LoaderArgs) {
  // params is typed
}

export default function Component({ loaderData }: Route.ComponentProps) {
  // loaderData is typed
}
```

### 2. Use Forms for Navigation-Causing Mutations

```tsx
// Good: Uses <Form> for main app actions
<Form method="post" action="/projects">
  <input name="title" />
  <button>Create</button>
</Form>
```

### 3. Use Fetchers for Non-Navigation Mutations

```tsx
// Good: Uses fetcher for secondary updates
const fetcher = useFetcher();

<fetcher.Form method="post" action="/task/123">
  <input name="status" />
  <button>Update</button>
</fetcher.Form>
```

### 4. Handle Validation Errors Properly

```tsx
// Return with 4xx status to prevent revalidation
if (validationFailed) {
  return data({ errors }, { status: 400 });
}
```

### 5. Use `redirect()` in Loaders/Actions

```tsx
// Prefer redirect in loaders/actions
if (!user) {
  return redirect("/login");
}

// Over useNavigate in components
const navigate = useNavigate(); // Reserve for rare cases
```

### 6. Leverage `useMatches()` for Layout Data

```tsx
// Access parent route data in nested components
const matches = useMatches();
const parentData = matches[matches.length - 2]?.data;
```

## Router Creation

### Data Mode (createBrowserRouter)

```tsx
import { createBrowserRouter, RouterProvider } from "react-router";

const router = createBrowserRouter([
  { path: "/", Component: Home },
  { path: "/about", Component: About },
]);

export default function App() {
  return <RouterProvider router={router} />;
}
```

### Framework Mode Setup

Use the framework convention with `routes.ts`:

```ts filename=app/routes.ts
import { route, type RouteConfig } from "@react-router/dev/routes";

export default [
  route("/", "./routes/index.tsx"),
  route("about", "./routes/about.tsx"),
  route("posts/:id", "./routes/post.tsx"),
] satisfies RouteConfig;
```

### Router Options

```tsx
const router = createBrowserRouter(routes, {
  basename: "/app",                    // URL base path
  future: { v7_... },                 // Future flags
  hydrationData: serverData,          // SSR hydration
  dataStrategy: customStrategy,       // Custom data loading
  getContext: createContext,          // Custom context
  patchRoutesOnNavigation: patchFn,  // Dynamic routes
  unstable_instrumentations: [logger] // Instrumentation
});
```

## Conclusion

React Router v7 provides a mature, type-safe routing solution with automatic TypeScript support. Its familiar API for v6 users, combined with modern features like middleware, server-side rendering support, and comprehensive type generation, makes it a strong choice for building scalable React applications.

The key advantage over alternatives is the seamless integration with existing React patterns (hooks, components) and the framework's approach to type safety through automatic code generation rather than manual annotations.




### Event Handlers

# Event Handler TypeScript Patterns

Proper event typing ensures type-safe access to event properties and target elements.

## Mouse Events

```typescript
// Click events
function handleClick(event: React.MouseEvent<HTMLButtonElement>) {
  event.preventDefault();
  event.stopPropagation();

  // Target element typed correctly
  event.currentTarget.disabled = true;
  event.currentTarget.textContent = 'Clicked';

  // Mouse position
  console.log(event.clientX, event.clientY);
  console.log(event.pageX, event.pageY);

  // Mouse buttons
  console.log(event.button); // 0 = left, 1 = middle, 2 = right
  console.log(event.buttons); // Bitmask of pressed buttons

  // Modifier keys
  if (event.ctrlKey || event.metaKey) {
    console.log('Ctrl/Cmd + Click');
  }
  if (event.shiftKey) {
    console.log('Shift + Click');
  }
  if (event.altKey) {
    console.log('Alt + Click');
  }
}

// Mouse movement
function handleMouseMove(event: React.MouseEvent<HTMLDivElement>) {
  const rect = event.currentTarget.getBoundingClientRect();
  const x = event.clientX - rect.left;
  const y = event.clientY - rect.top;
  console.log(`Position in element: ${x}, ${y}`);
}

// Hover events
function handleMouseEnter(event: React.MouseEvent<HTMLDivElement>) {
  event.currentTarget.style.backgroundColor = 'lightblue';
}

function handleMouseLeave(event: React.MouseEvent<HTMLDivElement>) {
  event.currentTarget.style.backgroundColor = '';
}

// Double click
function handleDoubleClick(event: React.MouseEvent<HTMLElement>) {
  console.log('Double clicked');
}
```

## Form Events

```typescript
// Form submission
function handleSubmit(event: React.FormEvent<HTMLFormElement>) {
  event.preventDefault();

  const form = event.currentTarget;
  const formData = new FormData(form);

  const data = {
    name: formData.get('name') as string,
    email: formData.get('email') as string,
  };

  console.log(data);
}

// Input change
function handleChange(event: React.ChangeEvent<HTMLInputElement>) {
  const target = event.target;

  // For text inputs
  if (target.type === 'text' || target.type === 'email') {
    console.log(target.value); // string
  }

  // For checkboxes
  if (target.type === 'checkbox') {
    console.log(target.checked); // boolean
  }

  // For radio buttons
  if (target.type === 'radio') {
    console.log(target.value, target.checked);
  }

  // For number inputs
  if (target.type === 'number') {
    console.log(target.valueAsNumber); // number
  }

  // For file inputs
  if (target.type === 'file') {
    const files = target.files; // FileList | null
    if (files && files.length > 0) {
      console.log(files[0].name);
    }
  }
}

// Textarea change
function handleTextareaChange(event: React.ChangeEvent<HTMLTextAreaElement>) {
  console.log(event.target.value);
  console.log(event.target.selectionStart); // Cursor position
}

// Select change
function handleSelectChange(event: React.ChangeEvent<HTMLSelectElement>) {
  const value = event.target.value;
  const selectedIndex = event.target.selectedIndex;
  const selectedOption = event.target.options[selectedIndex];
  console.log(value, selectedOption.text);
}

// Input events (fires on every keystroke)
function handleInput(event: React.FormEvent<HTMLInputElement>) {
  console.log(event.currentTarget.value);
}

// Reset event
function handleReset(event: React.FormEvent<HTMLFormElement>) {
  event.preventDefault();
  console.log('Form reset');
}
```

## Keyboard Events

```typescript
function handleKeyDown(event: React.KeyboardEvent<HTMLInputElement>) {
  // Key identification
  console.log(event.key); // 'Enter', 'Escape', 'a', etc.
  console.log(event.code); // 'Enter', 'Escape', 'KeyA', etc.

  // Common patterns
  if (event.key === 'Enter') {
    event.preventDefault();
    console.log('Enter pressed');
  }

  if (event.key === 'Escape') {
    event.currentTarget.blur();
  }

  // Arrow keys
  if (event.key === 'ArrowUp') {
    event.preventDefault();
    // Navigate up
  }

  // Modifier keys
  if (event.ctrlKey && event.key === 's') {
    event.preventDefault();
    console.log('Ctrl+S - Save');
  }

  if (event.metaKey && event.key === 'k') {
    event.preventDefault();
    console.log('Cmd+K - Search');
  }

  // Check multiple modifiers
  if (event.ctrlKey && event.shiftKey && event.key === 'P') {
    event.preventDefault();
    console.log('Ctrl+Shift+P - Command palette');
  }

  // Key combinations
  if ((event.ctrlKey || event.metaKey) && event.key === 'Enter') {
    console.log('Submit with Ctrl/Cmd+Enter');
  }
}

function handleKeyUp(event: React.KeyboardEvent<HTMLInputElement>) {
  console.log('Key released:', event.key);
}

function handleKeyPress(event: React.KeyboardEvent<HTMLInputElement>) {
  // Deprecated - use keyDown instead
  console.log('Key pressed:', event.key);
}
```

## Focus Events

```typescript
function handleFocus(event: React.FocusEvent<HTMLInputElement>) {
  // Select all text on focus
  event.target.select();

  // Add visual indicator
  event.currentTarget.classList.add('focused');
}

function handleBlur(event: React.FocusEvent<HTMLInputElement>) {
  // Validate on blur
  const value = event.target.value;
  if (value === '') {
    event.currentTarget.classList.add('error');
  }

  // Remove visual indicator
  event.currentTarget.classList.remove('focused');
}

// Focus within
function handleFocusWithin(event: React.FocusEvent<HTMLDivElement>) {
  // Related target - element receiving focus
  const relatedTarget = event.relatedTarget as HTMLElement | null;
  console.log('Focus moved from:', relatedTarget);
}
```

## Drag and Drop Events

```typescript
function handleDragStart(event: React.DragEvent<HTMLDivElement>) {
  event.dataTransfer.effectAllowed = 'move';
  event.dataTransfer.setData('text/plain', event.currentTarget.id);

  // Custom drag image
  const dragImage = document.createElement('div');
  dragImage.textContent = 'Dragging...';
  event.dataTransfer.setDragImage(dragImage, 0, 0);
}

function handleDragOver(event: React.DragEvent<HTMLDivElement>) {
  event.preventDefault();
  event.dataTransfer.dropEffect = 'move';
  event.currentTarget.classList.add('drag-over');
}

function handleDragLeave(event: React.DragEvent<HTMLDivElement>) {
  event.currentTarget.classList.remove('drag-over');
}

function handleDrop(event: React.DragEvent<HTMLDivElement>) {
  event.preventDefault();
  event.currentTarget.classList.remove('drag-over');

  const data = event.dataTransfer.getData('text/plain');
  console.log('Dropped:', data);

  // Handle files
  const files = event.dataTransfer.files;
  if (files.length > 0) {
    Array.from(files).forEach((file) => {
      console.log(file.name, file.type, file.size);
    });
  }
}

function handleDragEnd(event: React.DragEvent<HTMLDivElement>) {
  console.log('Drag ended');
}
```

## Clipboard Events

```typescript
function handleCopy(event: React.ClipboardEvent<HTMLDivElement>) {
  event.preventDefault();

  // Custom copy behavior
  const selection = window.getSelection();
  if (selection) {
    const text = `Copied from app: ${selection.toString()}`;
    event.clipboardData.setData('text/plain', text);
  }
}

function handleCut(event: React.ClipboardEvent<HTMLInputElement>) {
  console.log('Cut:', event.currentTarget.value);
}

function handlePaste(event: React.ClipboardEvent<HTMLInputElement>) {
  event.preventDefault();

  const pastedText = event.clipboardData.getData('text/plain');
  console.log('Pasted:', pastedText);

  // Validate pasted content
  const sanitized = pastedText.replace(/[^a-zA-Z0-9]/g, '');
  event.currentTarget.value = sanitized;
}
```

## Composition Events (IME)

```typescript
// For international input methods (Chinese, Japanese, etc.)
function handleCompositionStart(event: React.CompositionEvent<HTMLInputElement>) {
  console.log('Composition started');
}

function handleCompositionUpdate(event: React.CompositionEvent<HTMLInputElement>) {
  console.log('Composing:', event.data);
}

function handleCompositionEnd(event: React.CompositionEvent<HTMLInputElement>) {
  console.log('Composition ended:', event.data);
}
```

## Touch Events

```typescript
function handleTouchStart(event: React.TouchEvent<HTMLDivElement>) {
  const touch = event.touches[0];
  console.log('Touch start:', touch.clientX, touch.clientY);
}

function handleTouchMove(event: React.TouchEvent<HTMLDivElement>) {
  event.preventDefault(); // Prevent scrolling

  const touch = event.touches[0];
  console.log('Touch move:', touch.clientX, touch.clientY);
}

function handleTouchEnd(event: React.TouchEvent<HTMLDivElement>) {
  console.log('Touch ended');
}

// Multi-touch
function handleMultiTouch(event: React.TouchEvent<HTMLDivElement>) {
  if (event.touches.length === 2) {
    const [touch1, touch2] = event.touches;

    const distance = Math.hypot(
      touch2.clientX - touch1.clientX,
      touch2.clientY - touch1.clientY
    );

    console.log('Pinch distance:', distance);
  }
}
```

## Wheel Events

```typescript
function handleWheel(event: React.WheelEvent<HTMLDivElement>) {
  // Prevent default scroll
  event.preventDefault();

  // Scroll delta
  console.log('Delta X:', event.deltaX);
  console.log('Delta Y:', event.deltaY);
  console.log('Delta Z:', event.deltaZ);

  // Delta mode (0 = pixels, 1 = lines, 2 = pages)
  console.log('Delta mode:', event.deltaMode);

  // Zoom with Ctrl+Wheel
  if (event.ctrlKey) {
    const zoomDelta = event.deltaY > 0 ? -0.1 : 0.1;
    console.log('Zoom:', zoomDelta);
  }
}
```

## Generic Event Handlers

Reusable handlers with proper typing.

```typescript
// Generic change handler
function createChangeHandler<T extends HTMLElement>(
  callback: (value: string) => void
) {
  return (event: React.ChangeEvent<T>) => {
    if ('value' in event.target) {
      callback(event.target.value);
    }
  };
}

// Usage
const handleNameChange = createChangeHandler<HTMLInputElement>((value) => {
  setName(value);
});

const handleBioChange = createChangeHandler<HTMLTextAreaElement>((value) => {
  setBio(value);
});

// Generic click handler with target validation
function createClickHandler<T extends HTMLElement>(
  selector: string,
  callback: (element: T) => void
) {
  return (event: React.MouseEvent<HTMLElement>) => {
    const target = event.target as HTMLElement;
    const match = target.closest(selector);

    if (match) {
      callback(match as T);
    }
  };
}

// Usage
const handleItemClick = createClickHandler<HTMLLIElement>('li[data-id]', (item) => {
  const id = item.dataset.id;
  console.log('Clicked item:', id);
});
```

## Event Handler Type Aliases

```typescript
// Create reusable type aliases
type InputChangeHandler = React.ChangeEventHandler<HTMLInputElement>;
type ButtonClickHandler = React.MouseEventHandler<HTMLButtonElement>;
type FormSubmitHandler = React.FormEventHandler<HTMLFormElement>;

// Usage
const handleChange: InputChangeHandler = (event) => {
  console.log(event.target.value);
};

const handleClick: ButtonClickHandler = (event) => {
  event.currentTarget.disabled = true;
};

const handleSubmit: FormSubmitHandler = (event) => {
  event.preventDefault();
};

// Generic handler type
type EventHandler<E extends HTMLElement, Evt extends React.SyntheticEvent> = (
  event: Evt & { currentTarget: E }
) => void;

// Usage
const handleInput: EventHandler<HTMLInputElement, React.ChangeEvent<HTMLInputElement>> = (
  event
) => {
  console.log(event.currentTarget.value);
};
```

## Delegated Event Handlers

Type-safe event delegation.

```typescript
function ListContainer() {
  const handleListClick = (event: React.MouseEvent<HTMLUListElement>) => {
    const target = event.target as HTMLElement;

    // Find clicked list item
    const listItem = target.closest('li');
    if (!listItem) return;

    // Type guard for data attributes
    const itemId = listItem.getAttribute('data-id');
    if (itemId) {
      console.log('Clicked item:', itemId);
    }

    // Handle button within list item
    if (target.matches('button.delete')) {
      event.stopPropagation();
      console.log('Delete clicked');
    }
  };

  return (
    <ul onClick={handleListClick}>
      <li data-id="1">
        Item 1<button className="delete">Delete</button>
      </li>
      <li data-id="2">
        Item 2<button className="delete">Delete</button>
      </li>
    </ul>
  );
}
```

## Native vs Synthetic Events

```typescript
function Component() {
  const ref = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const element = ref.current;
    if (!element) return;

    // Native event listener
    const handleNativeClick = (event: MouseEvent) => {
      console.log('Native click:', event.target);
    };

    element.addEventListener('click', handleNativeClick);

    return () => {
      element.removeEventListener('click', handleNativeClick);
    };
  }, []);

  // React synthetic event
  const handleReactClick = (event: React.MouseEvent<HTMLDivElement>) => {
    console.log('React click:', event.target);

    // Access native event
    const nativeEvent = event.nativeEvent;
    console.log('Native event:', nativeEvent);
  };

  return <div ref={ref} onClick={handleReactClick}>Click me</div>;
}
```

## Custom Events

```typescript
// Define custom event type
type CustomEventMap = {
  'user:login': CustomEvent<{ userId: string }>;
  'user:logout': CustomEvent;
};

// Typed custom event dispatcher
function dispatchCustomEvent<K extends keyof CustomEventMap>(
  element: HTMLElement,
  type: K,
  detail?: CustomEventMap[K]['detail']
) {
  const event = new CustomEvent(type, { detail, bubbles: true });
  element.dispatchEvent(event);
}

// Component using custom events
function UserButton() {
  const ref = useRef<HTMLButtonElement>(null);

  const handleLogin = () => {
    if (ref.current) {
      dispatchCustomEvent(ref.current, 'user:login', { userId: '123' });
    }
  };

  useEffect(() => {
    const element = ref.current;
    if (!element) return;

    const handleCustomLogin = (event: Event) => {
      const customEvent = event as CustomEvent<{ userId: string }>;
      console.log('User logged in:', customEvent.detail.userId);
    };

    element.addEventListener('user:login', handleCustomLogin);

    return () => {
      element.removeEventListener('user:login', handleCustomLogin);
    };
  }, []);

  return <button ref={ref} onClick={handleLogin}>Login</button>;
}
```




### Tanstack Router

# TanStack Router TypeScript Patterns

TanStack Router provides full type safety for routes, params, search params, and loader data.

## Basic Route Definition

```typescript
import { createRoute, createRootRoute } from '@tanstack/react-router';

// Root route
const rootRoute = createRootRoute({
  component: RootLayout,
});

// Basic route
const indexRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/',
  component: HomePage,
});

// Route tree
const routeTree = rootRoute.addChildren([indexRoute]);

// Router
const router = createRouter({ routeTree });

// Type for router - use in app
type Router = typeof router;
```

## Route with Params

```typescript
import { createRoute } from '@tanstack/react-router';
import { useParams } from '@tanstack/react-router';

const userRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/users/$userId',
  component: UserPage,
});

function UserPage() {
  const { userId } = useParams({ from: userRoute.id });
  // userId is typed as string

  return <div>User ID: {userId}</div>;
}

// Multiple params
const postRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/users/$userId/posts/$postId',
  component: PostPage,
});

function PostPage() {
  const { userId, postId } = useParams({ from: postRoute.id });
  // Both typed as string

  return <div>Post {postId} by user {userId}</div>;
}
```

## Search Params with Validation

```typescript
import { z } from 'zod';

const productsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/products',
  component: ProductsPage,
  validateSearch: z.object({
    category: z.string().optional(),
    sortBy: z.enum(['price', 'name', 'rating']).default('name'),
    page: z.number().int().positive().default(1),
    perPage: z.number().int().positive().default(20),
  }),
});

function ProductsPage() {
  const search = useSearch({ from: productsRoute.id });
  // search is typed from Zod schema:
  // {
  //   category?: string;
  //   sortBy: 'price' | 'name' | 'rating';
  //   page: number;
  //   perPage: number;
  // }

  return (
    <div>
      Category: {search.category || 'All'}
      Sort by: {search.sortBy}
      Page: {search.page}
    </div>
  );
}
```

## Loader Data

```typescript
type User = {
  id: string;
  name: string;
  email: string;
};

const userRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/users/$userId',
  component: UserPage,
  loader: async ({ params }) => {
    const user = await fetchUser(params.userId);
    return { user };
  },
});

function UserPage() {
  const { user } = useLoaderData({ from: userRoute.id });
  // user typed as User

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}

async function fetchUser(id: string): Promise<User> {
  const res = await fetch(`/api/users/${id}`);
  return res.json();
}
```

## Loader with Search Params

```typescript
const productsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/products',
  component: ProductsPage,
  validateSearch: z.object({
    category: z.string().optional(),
    page: z.number().default(1),
  }),
  loader: async ({ search }) => {
    // search is typed from validateSearch
    const products = await fetchProducts({
      category: search.category,
      page: search.page,
    });

    return { products };
  },
});

function ProductsPage() {
  const { products } = useLoaderData({ from: productsRoute.id });
  const search = useSearch({ from: productsRoute.id });

  return (
    <div>
      <h1>Products - {search.category || 'All'}</h1>
      <ul>
        {products.map((p) => (
          <li key={p.id}>{p.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

## Type-Safe Navigation

```typescript
import { useNavigate, Link } from '@tanstack/react-router';

function ProductList() {
  const navigate = useNavigate();

  const goToProduct = (productId: string) => {
    navigate({
      to: '/products/$productId',
      params: { productId }, // Type-checked
      search: { tab: 'reviews' }, // Type-checked against validateSearch
    });
  };

  return (
    <div>
      {products.map((product) => (
        <Link
          key={product.id}
          to="/products/$productId"
          params={{ productId: product.id }}
          search={{ tab: 'details' }}
        >
          {product.name}
        </Link>
      ))}
    </div>
  );
}
```

## Search Params Manipulation

```typescript
import { useNavigate, useSearch } from '@tanstack/react-router';

const productsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/products',
  validateSearch: z.object({
    category: z.string().optional(),
    sortBy: z.enum(['price', 'name']).default('name'),
    page: z.number().default(1),
  }),
});

function ProductFilters() {
  const navigate = useNavigate({ from: productsRoute.id });
  const search = useSearch({ from: productsRoute.id });

  const updateCategory = (category: string) => {
    navigate({
      search: (prev) => ({
        ...prev,
        category, // Type-checked
        page: 1, // Reset page
      }),
    });
  };

  const updateSort = (sortBy: 'price' | 'name') => {
    navigate({
      search: (prev) => ({ ...prev, sortBy }),
    });
  };

  return (
    <div>
      <select value={search.category || ''} onChange={(e) => updateCategory(e.target.value)}>
        <option value="">All Categories</option>
        <option value="electronics">Electronics</option>
        <option value="books">Books</option>
      </select>

      <select value={search.sortBy} onChange={(e) => updateSort(e.target.value as 'price' | 'name')}>
        <option value="name">Name</option>
        <option value="price">Price</option>
      </select>
    </div>
  );
}
```

## Nested Routes

```typescript
const usersRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/users',
  component: UsersLayout,
});

const usersIndexRoute = createRoute({
  getParentRoute: () => usersRoute,
  path: '/',
  component: UsersList,
});

const userDetailRoute = createRoute({
  getParentRoute: () => usersRoute,
  path: '$userId',
  component: UserDetail,
});

const routeTree = rootRoute.addChildren([
  usersRoute.addChildren([
    usersIndexRoute,
    userDetailRoute,
  ]),
]);

// Layout component with outlet
function UsersLayout() {
  return (
    <div>
      <h1>Users</h1>
      <Outlet /> {/* Renders child route */}
    </div>
  );
}
```

## Before Load Hook

```typescript
const protectedRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/dashboard',
  beforeLoad: async ({ location }) => {
    const isAuthenticated = await checkAuth();

    if (!isAuthenticated) {
      throw redirect({
        to: '/login',
        search: { redirect: location.href },
      });
    }

    return { user: await getCurrentUser() };
  },
  component: Dashboard,
});

function Dashboard() {
  const { user } = useLoaderData({ from: protectedRoute.id });
  // user available from beforeLoad

  return <div>Welcome, {user.name}</div>;
}
```

## Error Handling

```typescript
const userRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/users/$userId',
  component: UserPage,
  errorComponent: UserErrorPage,
  loader: async ({ params }) => {
    const user = await fetchUser(params.userId);
    if (!user) {
      throw new Error('User not found');
    }
    return { user };
  },
});

function UserErrorPage({ error }: { error: Error }) {
  return (
    <div>
      <h1>Error</h1>
      <p>{error.message}</p>
      <Link to="/users">Back to users</Link>
    </div>
  );
}
```

## Pending Component

```typescript
const userRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/users/$userId',
  component: UserPage,
  pendingComponent: UserPageSkeleton,
  loader: async ({ params }) => {
    const user = await fetchUser(params.userId);
    return { user };
  },
});

function UserPageSkeleton() {
  return (
    <div>
      <div className="skeleton-header" />
      <div className="skeleton-body" />
    </div>
  );
}
```

## Router Context

Share data across all routes.

```typescript
type RouterContext = {
  auth: { user: User | null };
  queryClient: QueryClient;
};

const rootRoute = createRootRoute<RouterContext>({
  component: RootLayout,
});

const router = createRouter({
  routeTree,
  context: {
    auth: { user: null },
    queryClient: new QueryClient(),
  },
});

// Access in route
const userRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/users/$userId',
  beforeLoad: ({ context }) => {
    // context typed as RouterContext
    console.log(context.auth.user);
  },
});
```

## Route Masks

Hide actual URL structure.

```typescript
const userRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/users/$userId',
});

// Navigate with mask
navigate({
  to: '/users/$userId',
  params: { userId: '123' },
  mask: {
    to: '/profile',
  },
});

// URL shows /profile but renders /users/123
```

## Search Param Middleware

Transform search params before validation.

```typescript
const productsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/products',
  validateSearch: z.object({
    tags: z.array(z.string()).default([]),
  }),
  loaderDeps: ({ search }) => ({ tags: search.tags }),
  loader: async ({ deps }) => {
    const products = await fetchProducts({ tags: deps.tags });
    return { products };
  },
});

// URL: /products?tags=electronics&tags=sale
// search.tags = ['electronics', 'sale']
```

## Type Helpers

```typescript
import type { RouteIds, RouteById } from '@tanstack/react-router';

// Get all route IDs
type AllRouteIds = RouteIds<typeof router>;

// Get specific route type
type UserRoute = RouteById<typeof router, '/users/$userId'>;

// Extract params type
type UserParams = UserRoute['types']['allParams'];

// Extract search type
type UserSearch = UserRoute['types']['fullSearchSchema'];
```

## Preloading Routes

```typescript
import { useRouter } from '@tanstack/react-router';

function ProductCard({ productId }: { productId: string }) {
  const router = useRouter();

  const preloadProduct = () => {
    router.preloadRoute({
      to: '/products/$productId',
      params: { productId },
    });
  };

  return (
    <Link
      to="/products/$productId"
      params={{ productId }}
      onMouseEnter={preloadProduct} // Preload on hover
    >
      View Product
    </Link>
  );
}
```

## Route Matching

```typescript
import { useMatches, useMatch } from '@tanstack/react-router';

function Navigation() {
  const matches = useMatches();
  // Array of all matched routes

  const userMatch = useMatch({ from: userRoute.id, shouldThrow: false });
  // userMatch is typed, null if not matched

  return (
    <nav>
      {matches.map((match) => (
        <span key={match.id}>{match.pathname}</span>
      ))}
    </nav>
  );
}
```

## File-Based Routing (Code Generation)

```typescript
// routes/__root.tsx
export const Route = createRootRoute({
  component: RootLayout,
});

// routes/index.tsx
export const Route = createFileRoute('/')({
  component: HomePage,
});

// routes/users/$userId.tsx
export const Route = createFileRoute('/users/$userId')({
  component: UserPage,
  loader: async ({ params }) => {
    const user = await fetchUser(params.userId);
    return { user };
  },
});

// Generate route tree with CLI
// npm run generate-routes

// Import generated routes
import { routeTree } from './routeTree.gen';
const router = createRouter({ routeTree });
```

## Integration with React Query

```typescript
import { queryOptions, useQuery } from '@tanstack/react-query';

const userQueryOptions = (userId: string) =>
  queryOptions({
    queryKey: ['user', userId],
    queryFn: () => fetchUser(userId),
  });

const userRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/users/$userId',
  loader: ({ context, params }) => {
    // Prefetch with React Query
    return context.queryClient.ensureQueryData(userQueryOptions(params.userId));
  },
  component: UserPage,
});

function UserPage() {
  const { userId } = useParams({ from: userRoute.id });

  // Use same query options in component
  const { data: user } = useQuery(userQueryOptions(userId));

  return <div>{user?.name}</div>;
}
```




---

## 🚀 Usage

**Reference this template:** `@skill-react-dev.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
