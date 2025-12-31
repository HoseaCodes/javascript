# Comprehensive Next.js Coding Standards & Style Guide

Based on the Airbnb JavaScript/React style guides and Next.js best practices, this is a comprehensive coding standard for all your Next.js applications.

## 📋 Table of Contents

1. [Project Setup](#project-setup)
2. [Configuration Files](#configuration-files)
3. [JavaScript/TypeScript Standards](#javascripttypescript-standards)
4. [React/JSX Standards](#reactjsx-standards)
5. [File & Directory Structure](#file--directory-structure)
6. [Import/Export Standards](#importexport-standards)
7. [Naming Conventions](#naming-conventions)
8. [Code Formatting](#code-formatting)
9. [Git Workflow](#git-workflow)
10. [Performance Guidelines](#performance-guidelines)

## 🚀 Project Setup

### Initial Project Creation

```bash
npx create-next-app@latest my-app
✔ Would you like to use TypeScript? ... Yes
✔ Would you like to use ESLint? ... Yes
✔ Would you like to use Tailwind CSS? ... Yes
✔ Would you like to use `src/` directory? ... Yes
✔ Would you like to use App Router? ... Yes
✔ Would you like to customize the default import alias (@/*)? ... No
```

### Required Dependencies

```bash
# Core development dependencies
npm install --save-dev \
  eslint-config-airbnb \
  eslint-config-airbnb-typescript \
  @typescript-eslint/eslint-plugin \
  @typescript-eslint/parser \
  eslint-config-prettier \
  prettier \
  prettier-plugin-tailwindcss \
  husky \
  lint-staged

# Install Airbnb peer dependencies
npx install-peerdeps --dev eslint-config-airbnb
```

## ⚙️ Configuration Files

### `.eslintrc.json`

```json
{
  "env": {
    "browser": true,
    "es2021": true,
    "node": true
  },
  "extends": [
    "airbnb",
    "airbnb-typescript",
    "airbnb/hooks",
    "next/core-web-vitals",
    "prettier"
  ],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "project": "./tsconfig.json",
    "ecmaVersion": 2021,
    "sourceType": "module"
  },
  "plugins": [
    "react",
    "@typescript-eslint"
  ],
  "rules": {
    "react/jsx-filename-extension": [
      2,
      { "extensions": [".js", ".jsx", ".ts", ".tsx"] }
    ],
    "react/function-component-definition": [
      2,
      {
        "namedComponents": "function-declaration",
        "unnamedComponents": "arrow-function"
      }
    ],
    "import/prefer-default-export": "off",
    "react/jsx-props-no-spreading": "off",
    "no-console": ["warn", { "allow": ["warn", "error"] }],
    "@typescript-eslint/no-unused-vars": ["error", { "argsIgnorePattern": "^_" }],
    "react/require-default-props": "off"
  }
}
```

### `.prettierrc.json`

```json
{
  "singleQuote": true,
  "semi": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 80,
  "bracketSpacing": true,
  "arrowParens": "avoid",
  "endOfLine": "lf",
  "plugins": ["prettier-plugin-tailwindcss"]
}
```

### `package.json` Scripts

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "lint:fix": "next lint --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "type-check": "tsc --noEmit",
    "prepare": "husky install"
  }
}
```

### Husky Configuration

```bash
# Initialize Husky
npx husky-init

# Pre-commit hook (.husky/pre-commit)
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx lint-staged
```

### `lint-staged` Configuration

```json
// package.json
{
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{json,md,css,scss}": [
      "prettier --write"
    ]
  }
}
```

## 📝 JavaScript/TypeScript Standards

### 1. Variable Declarations

```typescript
// ✅ Good - Use const for all references
const userName = 'john_doe';
const userConfig = { theme: 'dark' };

// ✅ Good - Use let when reassignment is needed
let currentPage = 1;
currentPage += 1;

// ❌ Bad - Avoid var
var globalVar = 'avoid this';
```

### 2. Function Declarations

```typescript
// ✅ Good - Named function declarations for top-level functions
function calculateTotal(items: Item[]): number {
  return items.reduce((total, item) => total + item.price, 0);
}

// ✅ Good - Arrow functions for callbacks and inline functions
const processedItems = items.map(item => ({
  ...item,
  processedAt: new Date(),
}));

// ✅ Good - Async/await over promises
async function fetchUserData(userId: string): Promise<User> {
  try {
    const response = await api.get(`/users/${userId}`);
    return response.data;
  } catch (error) {
    console.error('Failed to fetch user data:', error);
    throw error;
  }
}
```

### 3. Object and Array Handling

```typescript
// ✅ Good - Object destructuring
function UserProfile({ name, email, avatar }: User) {
  // component logic
}

// ✅ Good - Array destructuring
const [isLoading, setIsLoading] = useState(false);

// ✅ Good - Spread operator
const updatedUser = { ...existingUser, email: newEmail };
const allItems = [...itemsA, ...itemsB];
```

### 4. Type Definitions

```typescript
// ✅ Good - Interface for object shapes
interface User {
  id: string;
  name: string;
  email: string;
  avatar?: string;
  createdAt: Date;
}

// ✅ Good - Type for unions and primitives
type Status = 'loading' | 'success' | 'error';
type ApiResponse<T> = {
  data: T;
  status: Status;
  message?: string;
};

// ✅ Good - Generic types
function createApiResponse<T>(data: T, status: Status): ApiResponse<T> {
  return { data, status };
}
```

## ⚛️ React/JSX Standards

### 1. Component Structure

```typescript
// ✅ Good - Function declaration for named components
interface UserCardProps {
  user: User;
  onEdit?: (user: User) => void;
  className?: string;
}

function UserCard({ user, onEdit, className }: UserCardProps) {
  const [isEditing, setIsEditing] = useState(false);

  const handleEdit = useCallback(() => {
    onEdit?.(user);
    setIsEditing(false);
  }, [user, onEdit]);

  return (
    <div className={cn('user-card', className)}>
      <img src={user.avatar} alt={`${user.name} avatar`} />
      <h3>{user.name}</h3>
      <p>{user.email}</p>
      {onEdit && (
        <button onClick={handleEdit} type="button">
          Edit
        </button>
      )}
    </div>
  );
}

export default UserCard;
```

### 2. Hooks Usage

```typescript
// ✅ Good - Custom hooks for reusable logic
function useUser(userId: string) {
  const [user, setUser] = useState<User | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    async function fetchUser() {
      try {
        setIsLoading(true);
        const userData = await fetchUserData(userId);
        setUser(userData);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Unknown error');
      } finally {
        setIsLoading(false);
      }
    }

    if (userId) {
      fetchUser();
    }
  }, [userId]);

  return { user, isLoading, error };
}
```

### 3. Event Handlers

```typescript
// ✅ Good - Proper event handler naming and typing
interface FormData {
  email: string;
  password: string;
}

function LoginForm() {
  const [formData, setFormData] = useState<FormData>({
    email: '',
    password: '',
  });

  const handleInputChange = (event: ChangeEvent<HTMLInputElement>) => {
    const { name, value } = event.target;
    setFormData(prev => ({ ...prev, [name]: value }));
  };

  const handleSubmit = async (event: FormEvent<HTMLFormElement>) => {
    event.preventDefault();
    // Handle form submission
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleInputChange}
        placeholder="Email"
        required
      />
      {/* More form fields */}
    </form>
  );
}
```

### 4. Conditional Rendering

```typescript
// ✅ Good - Explicit conditions
function UserDashboard({ user }: { user: User | null }) {
  if (!user) {
    return <LoginPrompt />;
  }

  return (
    <div className="dashboard">
      <h1>Welcome, {user.name}!</h1>
      {user.isAdmin && <AdminPanel />}
      {user.notifications.length > 0 && (
        <NotificationList notifications={user.notifications} />
      )}
    </div>
  );
}
```

## 📁 File & Directory Structure

```
src/
├── app/                          # Next.js App Router
│   ├── (auth)/                   # Route groups
│   │   ├── login/
│   │   └── register/
│   ├── dashboard/
│   │   ├── page.tsx
│   │   ├── loading.tsx
│   │   └── error.tsx
│   ├── layout.tsx                # Root layout
│   ├── page.tsx                  # Homepage
│   ├── globals.css
│   └── not-found.tsx
├── components/                   # Reusable UI components
│   ├── ui/                       # Basic UI components
│   │   ├── Button.tsx
│   │   ├── Input.tsx
│   │   └── index.ts              # Barrel exports
│   ├── forms/                    # Form components
│   │   ├── LoginForm.tsx
│   │   └── ContactForm.tsx
│   └── layout/                   # Layout components
│       ├── Header.tsx
│       ├── Footer.tsx
│       └── Navigation.tsx
├── lib/                          # Utility functions and configurations
│   ├── api.ts                    # API client setup
│   ├── auth.ts                   # Authentication utilities
│   ├── utils.ts                  # General utilities
│   └── validations.ts            # Form validation schemas
├── hooks/                        # Custom React hooks
│   ├── useAuth.ts
│   ├── useApi.ts
│   └── useLocalStorage.ts
├── types/                        # TypeScript type definitions
│   ├── api.ts
│   ├── user.ts
│   └── index.ts
├── constants/                    # Application constants
│   ├── routes.ts
│   ├── config.ts
│   └── api-endpoints.ts
└── styles/                       # Global styles and Tailwind
    ├── globals.css
    └── components.css
```

## 📥 Import/Export Standards

### 1. Import Order

```typescript
// ✅ Good - Import order: external packages, internal modules, relative imports
import { useState, useEffect, useCallback } from 'react';
import { NextPage } from 'next';
import Link from 'next/link';
import { clsx } from 'clsx';

import { Button, Input } from '@/components/ui';
import { useAuth } from '@/hooks/useAuth';
import { User } from '@/types';
import { validateEmail } from '@/lib/validations';

import styles from './LoginForm.module.css';
```

### 2. Export Conventions

```typescript
// ✅ Good - Named exports for utilities
export function formatCurrency(amount: number): string {
  return new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD',
  }).format(amount);
}

export function truncateText(text: string, maxLength: number): string {
  return text.length <= maxLength ? text : `${text.slice(0, maxLength)}...`;
}

// ✅ Good - Default export for components
function UserProfile({ user }: UserProfileProps) {
  // component logic
}

export default UserProfile;

// ✅ Good - Barrel exports for cleaner imports
// components/ui/index.ts
export { default as Button } from './Button';
export { default as Input } from './Input';
export { default as Modal } from './Modal';
```

## 🏷️ Naming Conventions

### 1. Files and Directories

```bash
# ✅ Good - Component files (PascalCase)
UserProfile.tsx
NavigationMenu.tsx
ApiService.ts

# ✅ Good - Directory names (kebab-case or camelCase)
user-management/
apiHelpers/
form-components/

# ✅ Good - Utility files (camelCase)
dateHelpers.ts
stringUtils.ts
apiClient.ts

# ✅ Good - Next.js special files
page.tsx
layout.tsx
loading.tsx
error.tsx
not-found.tsx
```

### 2. Variables and Functions

```typescript
// ✅ Good - Variables (camelCase)
const userName = 'john_doe';
const isUserLoggedIn = false;
const userPreferences = {};

// ✅ Good - Functions (camelCase, descriptive)
function calculateTotalPrice(items: Item[]): number { }
function validateEmailAddress(email: string): boolean { }
function formatDateToString(date: Date): string { }

// ✅ Good - Constants (UPPER_SNAKE_CASE)
const API_BASE_URL = 'https://api.example.com';
const MAX_RETRY_ATTEMPTS = 3;
const DEFAULT_PAGE_SIZE = 20;

// ✅ Good - Types and Interfaces (PascalCase)
interface UserProfile { }
type ApiResponse<T> = { };
enum UserRole { }
```

### 3. Component Props

```typescript
// ✅ Good - Props interface naming
interface UserCardProps {
  user: User;
  isEditable?: boolean;
  onEdit?: (user: User) => void;
  className?: string;
}

interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  isLoading?: boolean;
}
```

## 🎨 Code Formatting

### 1. JSX Formatting

```typescript
// ✅ Good - Multi-line props alignment
<Button
  type="submit"
  variant="primary"
  size="lg"
  onClick={handleSubmit}
  disabled={isLoading}
  className="w-full"
>
  {isLoading ? 'Submitting...' : 'Submit'}
</Button>

// ✅ Good - Conditional rendering
{isError && (
  <div className="error-message">
    <p>{error.message}</p>
  </div>
)}

// ✅ Good - List rendering
{users.map(user => (
  <UserCard
    key={user.id}
    user={user}
    onEdit={handleUserEdit}
  />
))}
```

### 2. Object and Array Formatting

```typescript
// ✅ Good - Object formatting
const userConfig = {
  name: 'John Doe',
  email: 'john@example.com',
  preferences: {
    theme: 'dark',
    notifications: true,
  },
};

// ✅ Good - Array formatting
const menuItems = [
  { id: 'home', label: 'Home', path: '/' },
  { id: 'about', label: 'About', path: '/about' },
  { id: 'contact', label: 'Contact', path: '/contact' },
];
```

## 🔄 Git Workflow

### 1. Commit Message Format

```bash
# Format: <type>(<scope>): <description>

# ✅ Examples
feat(auth): add login functionality
fix(ui): resolve button styling issue
docs(readme): update installation instructions
refactor(api): simplify user data fetching
test(components): add UserCard component tests
style(formatting): apply prettier formatting
```

### 2. Branch Naming

```bash
# ✅ Good branch names
feature/user-authentication
fix/login-validation-bug
docs/setup-instructions
refactor/api-client-structure
```

## 🚀 Performance Guidelines

### 1. Component Optimization

```typescript
// ✅ Good - Memoization for expensive computations
const UserList = memo(function UserList({ users, searchTerm }: UserListProps) {
  const filteredUsers = useMemo(
    () => users.filter(user => 
      user.name.toLowerCase().includes(searchTerm.toLowerCase())
    ),
    [users, searchTerm]
  );

  return (
    <div>
      {filteredUsers.map(user => (
        <UserCard key={user.id} user={user} />
      ))}
    </div>
  );
});
```

### 2. Next.js Specific Optimizations

```typescript
// ✅ Good - Dynamic imports for code splitting
const DynamicComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <LoadingSpinner />,
  ssr: false,
});

// ✅ Good - Image optimization
import Image from 'next/image';

function UserAvatar({ user }: { user: User }) {
  return (
    <Image
      src={user.avatar}
      alt={`${user.name} avatar`}
      width={100}
      height={100}
      className="rounded-full"
    />
  );
}
```

## 🧪 Testing Standards

```typescript
// ✅ Good - Component testing example
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from './Button';

describe('Button', () => {
  it('renders with correct text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('calls onClick when clicked', () => {
    const mockOnClick = jest.fn();
    render(<Button onClick={mockOnClick}>Click me</Button>);
    
    fireEvent.click(screen.getByText('Click me'));
    expect(mockOnClick).toHaveBeenCalledTimes(1);
  });

  it('shows loading state correctly', () => {
    render(<Button isLoading>Submit</Button>);
    expect(screen.getByText('Loading...')).toBeInTheDocument();
  });
});
```

This comprehensive style guide should serve as your foundation for all Next.js projects. Remember to adapt specific rules based on your project needs while maintaining consistency across your applications.
