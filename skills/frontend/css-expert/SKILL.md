---
name: css-expert
description: Expert in modern CSS, Tailwind CSS, responsive design, animations, and CSS architecture. Use for styling, layout, and design implementation.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# CSS & Styling Expert

## Purpose
Provide expert-level CSS development assistance including modern CSS features, Tailwind CSS, responsive design, animations, and maintainable CSS architecture.

## When to Use This Skill
- Creating responsive layouts with Flexbox and Grid
- Implementing custom designs with CSS
- Tailwind CSS configuration and usage
- CSS animations and transitions
- CSS-in-JS solutions
- Design system implementation
- Performance optimization
- Cross-browser compatibility

## Modern CSS Features

### 1. CSS Grid
```css
.grid-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
  padding: 1rem;
}

.grid-item {
  grid-column: span 2;
}
```

### 2. Flexbox
```css
.flex-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 1rem;
  flex-wrap: wrap;
}
```

### 3. Custom Properties
```css
:root {
  --primary-color: #3b82f6;
  --spacing-unit: 0.5rem;
  --border-radius: 0.375rem;
}

.component {
  color: var(--primary-color);
  padding: calc(var(--spacing-unit) * 2);
  border-radius: var(--border-radius);
}
```

### 4. Container Queries
```css
.card-container {
  container-type: inline-size;
  container-name: card;
}

@container card (min-width: 400px) {
  .card-title {
    font-size: 2rem;
  }
}
```

## Responsive Design

### Mobile-First Approach
```css
/* Mobile styles (default) */
.container {
  padding: 1rem;
}

/* Tablet and up */
@media (min-width: 768px) {
  .container {
    padding: 2rem;
  }
}

/* Desktop and up */
@media (min-width: 1024px) {
  .container {
    padding: 3rem;
    max-width: 1200px;
    margin: 0 auto;
  }
}
```

## Tailwind CSS Patterns

### Component Composition
```html
<div class="flex items-center justify-between p-4 bg-white rounded-lg shadow-md">
  <h2 class="text-xl font-semibold text-gray-800">Title</h2>
  <button class="px-4 py-2 text-white bg-blue-500 rounded hover:bg-blue-600 transition-colors">
    Action
  </button>
</div>
```

### Custom Tailwind Configuration
```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        brand: {
          50: '#f0f9ff',
          500: '#3b82f6',
          900: '#1e3a8a'
        }
      },
      spacing: {
        '128': '32rem',
      }
    }
  }
}
```

## Animations

### CSS Transitions
```css
.button {
  transition: all 0.3s ease-in-out;
}

.button:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}
```

### CSS Animations
```css
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.element {
  animation: fadeInUp 0.6s ease-out;
}
```

## CSS Architecture

### BEM Methodology
```css
/* Block */
.card { }

/* Element */
.card__title { }
.card__content { }

/* Modifier */
.card--featured { }
.card__title--large { }
```

### CSS Modules
```css
/* component.module.css */
.container {
  padding: 1rem;
}

.title {
  font-size: 1.5rem;
  color: var(--primary);
}
```

## Best Practices

1. **Mobile-First** - Start with mobile styles, enhance for larger screens
2. **Use Modern Features** - Leverage Grid, Flexbox, Custom Properties
3. **Performance** - Minimize repaints, use will-change sparingly
4. **Accessibility** - Ensure sufficient contrast, focus states
5. **Maintainability** - Use consistent naming, avoid deep nesting
6. **Responsive Images** - Use srcset and picture elements
7. **Dark Mode** - Support prefers-color-scheme

This skill ensures modern, responsive, and maintainable styling solutions.
