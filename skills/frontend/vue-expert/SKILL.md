---
name: vue-expert
description: Expert in Vue.js 3 development with Composition API, TypeScript, Pinia, and modern patterns. Use for Vue component development and best practices.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# Vue.js 3 Development Expert

## Purpose
Provide expert-level Vue.js 3 development assistance focusing on Composition API, TypeScript integration, state management with Pinia, and modern Vue patterns.

## When to Use This Skill
- Building Vue 3 components with Composition API
- TypeScript integration with Vue
- State management with Pinia
- Vue Router setup and configuration
- Form handling and validation
- Composables development
- Performance optimization
- Testing Vue components

## Key Principles

### 1. Composition API First
- Prefer Composition API over Options API
- Use `<script setup>` syntax
- Create reusable composables
- Leverage TypeScript with Vue

### 2. Reactivity System
- Understand ref vs reactive
- Use computed for derived state
- Watch for side effects
- toRefs for destructuring

### 3. Component Design
- Single File Components (SFC)
- Props and emits with TypeScript
- Provide/inject for dependency injection
- Scoped slots for flexibility

## Component Template

```vue
<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue';

interface Props {
  data: DataType;
  title?: string;
}

interface Emits {
  (e: 'update', value: string): void;
  (e: 'delete', id: number): void;
}

const props = withDefaults(defineProps<Props>(), {
  title: 'Default Title'
});

const emit = defineEmits<Emits>();

const state = ref<StateType>({
  loading: false,
  items: []
});

const computedValue = computed(() => {
  return props.data.map(item => item.value);
});

watch(() => props.data, (newVal, oldVal) => {
  // Handle data changes
}, { deep: true });

onMounted(() => {
  // Component mounted
});

const handleAction = () => {
  emit('update', 'new value');
};
</script>

<template>
  <div class="container">
    <h1>{{ title }}</h1>
    <button @click="handleAction">Action</button>
  </div>
</template>

<style scoped>
.container {
  padding: 1rem;
}
</style>
```

## Composables Pattern

```typescript
// useData.ts
import { ref, Ref } from 'vue';

export function useData<T>(initialValue: T) {
  const data: Ref<T> = ref(initialValue);
  const loading = ref(false);
  const error = ref<Error | null>(null);

  const fetchData = async (url: string) => {
    loading.value = true;
    error.value = null;
    try {
      const response = await fetch(url);
      data.value = await response.json();
    } catch (err) {
      error.value = err as Error;
    } finally {
      loading.value = false;
    }
  };

  return {
    data,
    loading,
    error,
    fetchData
  };
}
```

## Pinia Store

```typescript
// stores/user.ts
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

export const useUserStore = defineStore('user', () => {
  const user = ref<User | null>(null);
  const isAuthenticated = computed(() => user.value !== null);

  const login = async (credentials: Credentials) => {
    const response = await api.login(credentials);
    user.value = response.user;
  };

  const logout = () => {
    user.value = null;
  };

  return {
    user,
    isAuthenticated,
    login,
    logout
  };
});
```

## Best Practices

1. **Use TypeScript** - Type safety for props, emits, and state
2. **Composables for Logic** - Extract reusable logic into composables
3. **Performance** - Use v-memo, KeepAlive, and lazy loading
4. **Scoped Styles** - Keep styles scoped to components
5. **Testing** - Use Vitest and Vue Test Utils

## Tools & Libraries

- **State Management**: Pinia
- **Routing**: Vue Router
- **Forms**: VeeValidate, Vuelidate
- **UI Libraries**: Vuetify, Element Plus, PrimeVue
- **Testing**: Vitest, Vue Test Utils
- **Build Tools**: Vite, Nuxt 3

This skill ensures modern, type-safe, and performant Vue.js applications.
