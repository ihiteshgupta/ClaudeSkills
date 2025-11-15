---
name: react-expert
description: Expert in React development with hooks, TypeScript, state management, and modern patterns. Use for React component development, optimization, and best practices.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# React Development Expert

## Purpose
Provide expert-level React development assistance focusing on modern React patterns, hooks, TypeScript integration, performance optimization, and best practices.

## When to Use This Skill
- Building React components with functional components and hooks
- Implementing complex state management (Context API, Redux, Zustand)
- TypeScript integration with React
- Performance optimization (memo, useMemo, useCallback)
- React Router implementation
- Form handling and validation
- API integration with React Query or SWR
- Testing React components

## Key Principles

### 1. Modern React Patterns
- Prefer functional components over class components
- Use hooks for state and side effects
- Implement custom hooks for reusable logic
- Follow component composition patterns

### 2. TypeScript Best Practices
- Define proper prop types with TypeScript interfaces
- Use generics for reusable components
- Leverage type inference where appropriate
- Create discriminated unions for complex state

### 3. State Management
- Use useState for local component state
- useReducer for complex state logic
- Context API for shared state
- Consider Redux Toolkit or Zustand for global state
- Implement proper state normalization

### 4. Performance Optimization
- Use React.memo for expensive components
- Implement useMemo for expensive calculations
- useCallback for function stability
- Code splitting with React.lazy and Suspense
- Virtualization for long lists

### 5. Side Effects
- useEffect for side effects with proper dependency arrays
- Clean up effects properly
- Avoid infinite loops
- Separate concerns in different effects

## Code Structure

### Component Template
```typescript
import React, { useState, useEffect, useMemo, useCallback } from 'react';

interface ComponentProps {
  data: DataType;
  onAction: (id: string) => void;
  className?: string;
}

export const Component: React.FC<ComponentProps> = ({
  data,
  onAction,
  className
}) => {
  const [state, setState] = useState<StateType>(initialState);

  const memoizedValue = useMemo(() => {
    return expensiveCalculation(data);
  }, [data]);

  const handleAction = useCallback((id: string) => {
    onAction(id);
  }, [onAction]);

  useEffect(() => {
    // Side effect
    return () => {
      // Cleanup
    };
  }, [dependencies]);

  return (
    <div className={className}>
      {/* Component JSX */}
    </div>
  );
};
```

### Custom Hook Template
```typescript
import { useState, useEffect } from 'react';

export function useCustomHook<T>(initialValue: T) {
  const [value, setValue] = useState<T>(initialValue);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    // Custom hook logic
  }, []);

  return { value, setValue, loading, error };
}
```

## Common Patterns

### 1. Data Fetching
```typescript
const useData = (url: string) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch(url);
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };
    fetchData();
  }, [url]);

  return { data, loading, error };
};
```

### 2. Form Handling
```typescript
const useForm = <T extends Record<string, any>>(initialValues: T) => {
  const [values, setValues] = useState<T>(initialValues);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});

  const handleChange = (name: keyof T, value: any) => {
    setValues(prev => ({ ...prev, [name]: value }));
  };

  const validate = () => {
    // Validation logic
    return Object.keys(errors).length === 0;
  };

  return { values, errors, handleChange, validate };
};
```

### 3. Context Pattern
```typescript
interface ContextValue {
  state: State;
  actions: Actions;
}

const Context = createContext<ContextValue | undefined>(undefined);

export const Provider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [state, setState] = useState<State>(initialState);

  const value = useMemo(() => ({
    state,
    actions: {
      update: (data: Data) => setState(prev => ({ ...prev, data }))
    }
  }), [state]);

  return <Context.Provider value={value}>{children}</Context.Provider>;
};

export const useContextValue = () => {
  const context = useContext(Context);
  if (!context) {
    throw new Error('useContextValue must be used within Provider');
  }
  return context;
};
```

## Best Practices

1. **Component Organization**
   - One component per file
   - Co-locate related files
   - Use index files for exports
   - Separate containers from presentational components

2. **Props Management**
   - Destructure props
   - Use default props sparingly (prefer default parameters)
   - Avoid prop drilling (use context or composition)

3. **Error Handling**
   - Use Error Boundaries for error catching
   - Handle async errors properly
   - Provide user-friendly error messages

4. **Testing**
   - Write tests for components
   - Use React Testing Library
   - Test user interactions, not implementation
   - Mock external dependencies

5. **Accessibility**
   - Use semantic HTML
   - Provide ARIA labels where needed
   - Ensure keyboard navigation
   - Test with screen readers

## Anti-Patterns to Avoid

- Modifying state directly
- Missing dependencies in useEffect
- Using indexes as keys in lists
- Too many useEffect hooks in one component
- Props drilling through many levels
- Overusing Context (causes unnecessary re-renders)
- Not cleaning up side effects

## Tools & Libraries

- **State Management**: Redux Toolkit, Zustand, Jotai, Recoil
- **Data Fetching**: React Query, SWR, Apollo Client
- **Forms**: React Hook Form, Formik
- **Routing**: React Router, TanStack Router
- **UI Libraries**: Material-UI, Chakra UI, shadcn/ui
- **Testing**: Jest, React Testing Library, Vitest
- **Build Tools**: Vite, Next.js, Create React App

## Implementation Approach

When implementing React features:

1. **Analyze Requirements** - Understand the component's purpose and data flow
2. **Design Component Structure** - Plan component hierarchy and state placement
3. **Implement with TypeScript** - Define types first, then implement logic
4. **Optimize Performance** - Add memoization only when needed
5. **Add Error Handling** - Handle edge cases and errors gracefully
6. **Write Tests** - Cover critical functionality
7. **Review & Refactor** - Ensure code quality and maintainability

## Examples

### Complex Form with Validation
```typescript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8)
});

type FormData = z.infer<typeof schema>;

export const LoginForm: React.FC = () => {
  const { register, handleSubmit, formState: { errors } } = useForm<FormData>({
    resolver: zodResolver(schema)
  });

  const onSubmit = async (data: FormData) => {
    // Handle submission
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} />
      {errors.email && <span>{errors.email.message}</span>}

      <input type="password" {...register('password')} />
      {errors.password && <span>{errors.password.message}</span>}

      <button type="submit">Login</button>
    </form>
  );
};
```

This skill ensures production-ready, maintainable, and performant React applications.
