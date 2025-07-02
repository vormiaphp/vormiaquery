# VormiaQuery

A universal query and mutation library for seamless data fetching and state management, designed for use with React, Vue, Svelte, Solid, and Qwik. Built for modern JavaScript projects and Laravel/VormiaPHP backends.

---

## 🚀 Installation (JavaScript/Frontend)

Install VormiaQueryJS using your favorite package manager:

### **npm**

```bash
npm install vormiaqueryjs
```

### **bun**

```bash
bun add vormiaqueryjs
```

### **yarn**

```bash
yarn add vormiaqueryjs
```

### **pnpm**

```bash
pnpm add vormiaqueryjs
```

### **deno** (experimental, not fully supported)

```ts
import vormiaqueryjs from "npm:vormiaqueryjs";
```

> ⚠️ VormiaQueryJS is not fully Deno-compatible due to Node.js built-ins. Use with caution.

---

## 🚨 Required Peer Dependencies

After installing `vormiaqueryjs`, you must also install the correct peer dependencies for your stack:

- React:
  - `npm install @tanstack/react-query`
  - `npm install @tanstack/eslint-plugin-query` (optional)
- Vue:
  - `npm install @tanstack/vue-query`
  - `npm install @tanstack/eslint-plugin-query` (optional)
- Svelte:
  - `npm install @tanstack/svelte-query`
  - `npm install @tanstack/eslint-plugin-query` (optional)
- Solid:
  - `npm install @tanstack/solid-query`
  - `npm install @tanstack/eslint-plugin-query` (optional)
- Qwik:

  - `npm install @builder.io/qwik`
  - `npm install @tanstack/eslint-plugin-query` (optional)

- Common dependency for all frameworks:
  - `npm install axios`

> **Note:** VormiaQuery no longer prompts you to install peer dependencies after installation. Please refer to the instructions above and install the required dependencies for your framework manually. This change improves compatibility with bun, pnpm, and other package managers.

---

## 🔐 Encryption & Key Management

VormiaQuery supports optional end-to-end encryption using RSA public/private key pairs.

### Key Generation

After installing `vormiaqueryjs`, you can generate a secure RSA key pair using the built-in CLI tool:

```bash
npx vormiaquery-gen-keys
```

Or, if installed globally:

```bash
vormiaquery-gen-keys
```

This will create two files in your project directory:

- `vormia_public.pem` (public key)
- `vormia_private.pem` (private key)

### Key Storage Recommendations

- **Backend (Laravel):**
  - Store the **public key** and **private key** in your `.env` or a secure config file.
  - Example:
    ```env
    VORMIA_PUBLIC_KEY="<contents of vormia_public.pem>"
    VORMIA_PRIVATE_KEY="<contents of vormia_private.pem>"
    ```
- **Frontend (SSR/Node.js):**
  - Store the **private key** and **public key** in your `.env` or config.
  - Example:
    ```env
    VORMIA_PRIVATE_KEY="<contents of vormia_private.pem>"
    VORMIA_PUBLIC_KEY="<contents of vormia_public.pem>"
    ```
- **Never expose your private key in client-side browser code!**

### Usage

- VormiaQuery can use these keys to encrypt requests and decrypt responses.
- Your Laravel backend should use the same keys to decrypt/encrypt data.
- See the VormiaQuery and Laravel documentation for integration details.

> **Note:** The `vormiaquery-gen-keys` CLI is included with the `vormiaqueryjs` package and is available after installation. You can run it with `npx vormiaquery-gen-keys` (locally) or `vormiaquery-gen-keys` (globally) to generate your RSA key pair for encryption support.

---

## 🛠️ Laravel Integration (Backend)

For Laravel backend integration with VormiaQueryJS, install the official Laravel package:

```bash
composer require vormiaphp/vormiaqueryphp
```

Follow the complete installation and usage instructions at:

- [GitHub Repository](https://github.com/vormiaphp/vormiaqueryphp)
- [Packagist Package](https://packagist.org/packages/vormiaphp/vormiaqueryphp)

The Laravel package provides middleware, helpers, and complete integration for encrypted API communication with VormiaQueryJS.

---

## Features

- 🚀 **Easy to use**: Simple API for GET, POST, PUT, DELETE operations
- 🔒 **Built-in Authentication**: Token-based auth with automatic handling
- 🔐 **Data Encryption**: Optional AES encryption for sensitive data
- ⚡ **Framework Agnostic**: Works with React, Vue, Svelte, Solid, Qwik
- 🛡️ **Error Handling**: Comprehensive error handling with custom error types
- 🧪 **Tested with Vitest**: Modern, fast JavaScript testing
- 🟩 **Pure JavaScript**: No TypeScript required

## Quick Start

### Environment Variables

VormiaQuery supports configuration through environment variables. Create a `.env` file in your project root:

```env
VORMIA_API_URL=https://your-api.com/api/v1
VORMIA_AUTH_TOKEN_KEY=auth_token
VORMIA_TIMEOUT=30000
VORMIA_WITH_CREDENTIALS=false
```

---

## Usage Examples

### React

```jsx
import React from "react";
import {
  VormiaQueryProvider,
  useVrmQuery,
  useVrmMutation,
} from "vormiaqueryjs";

function App() {
  return (
    <VormiaQueryProvider config={{ baseURL: "https://api.example.com" }}>
      <CategoriesList />
    </VormiaQueryProvider>
  );
}

function CategoriesList() {
  const { data, isLoading, error, refetch } = useVrmQuery({
    endpoint: "/categories",
    method: "GET",
  });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <ul>
      {data?.response?.map((cat) => (
        <li key={cat.id}>{cat.name}</li>
      ))}
    </ul>
  );
}
```

### Vue

```js
<script setup>
import { useVormiaQuery } from 'vormiaqueryjs/adapters/vue';

const { data, error, isLoading, refetch } = useVormiaQuery({
  endpoint: '/categories',
  method: 'GET',
});
</script>

<template>
  <div v-if="isLoading">Loading...</div>
  <div v-else-if="error">Error: {{ error.message }}</div>
  <ul v-else>
    <li v-for="cat in data?.response" :key="cat.id">{{ cat.name }}</li>
  </ul>
</template>
```

### Svelte

```svelte
<script>
  import { createVormiaStore } from 'vormiaqueryjs/adapters/svelte';
  const store = createVormiaStore({ endpoint: '/categories', method: 'GET' });
</script>

{#if $store.loading}
  <p>Loading...</p>
{:else if $store.error}
  <p>Error: {$store.error.message}</p>
{:else}
  <ul>
    {#each $store.data?.response as cat}
      <li>{cat.name}</li>
    {/each}
  </ul>
{/if}
```

### Solid

```js
import { createVormiaResource } from "vormiaqueryjs/adapters/solid";

const [categories] = createVormiaResource({
  endpoint: "/categories",
  method: "GET",
});

function CategoriesList() {
  return (
    <ul>
      {categories()?.response?.map((cat) => (
        <li>{cat.name}</li>
      ))}
    </ul>
  );
}
```

### Qwik

```js
import { useVormiaQuery } from "vormiaqueryjs/adapters/qwik";

export default function CategoriesList() {
  const { data, isLoading, error } = useVormiaQuery({
    endpoint: "/categories",
    method: "GET",
  });
  // Render logic for Qwik
}
```

---

## Authentication Example (React)

```jsx
import { useVrmAuth } from "vormiaqueryjs";

function LoginForm() {
  const { login, isLoading, error, isAuthenticated } = useVrmAuth({
    endpoint: "/auth/login",
    encryptData: true,
    storeToken: true,
    onLoginSuccess: (data) => {
      console.log("Login successful:", data.user);
    },
  });

  const handleLogin = async (credentials) => {
    try {
      await login(credentials);
    } catch (err) {
      console.error("Login failed:", err);
    }
  };

  if (isAuthenticated) return <div>Welcome back!</div>;

  return (
    <form
      onSubmit={(e) => {
        e.preventDefault();
        handleLogin({ email: "user@example.com", password: "password" });
      }}
    >
      <button type="submit" disabled={isLoading}>
        {isLoading ? "Logging in..." : "Login"}
      </button>
    </form>
  );
}
```

---

## Mutations Example (React)

```jsx
import { useVrmMutation } from "vormiaqueryjs";

function AddCategory() {
  const mutation = useVrmMutation({ endpoint: "/categories", method: "POST" });

  const handleAdd = async () => {
    await mutation.mutate({ name: "New Category" });
  };

  return <button onClick={handleAdd}>Add Category</button>;
}
```

---

## Error Handling

```js
try {
  await mutation.mutateAsync(data);
} catch (error) {
  if (error.isValidationError()) {
    // Handle validation errors
  } else if (error.isUnauthorized()) {
    // Handle auth errors
  } else if (error.isServerError()) {
    // Handle server errors
  }
}
```

---

## Testing

This project uses [Vitest](https://vitest.dev/) for all tests. Example:

```bash
npm test
```

- All tests are written in JavaScript.
- No TypeScript or Jest is required.

---

## API Reference

- See the source code and examples above for API usage for each framework.
- All hooks and helpers are available under their respective framework adapter paths.

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

## Extra Usage Examples

### 1. Encrypted Mutation (React)

```jsx
import { useVrmMutation } from "vormiaqueryjs";

function SendSecret() {
  const mutation = useVrmMutation({
    endpoint: "/secret",
    method: "POST",
    rsaEncrypt: true, // Enable RSA encryption
  });

  const handleSend = async () => {
    await mutation.mutate({ message: "Top Secret" });
  };

  return <button onClick={handleSend}>Send Secret</button>;
}
```

### 2. Decrypting an Encrypted Response (Node.js/SSR)

```js
import { VormiaClient } from "vormiaqueryjs";

const client = new VormiaClient({
  baseURL: "https://api.example.com",
  rsaEncrypt: true,
  privateKey: process.env.VORMIA_PRIVATE_KEY,
  publicKey: process.env.VORMIA_PUBLIC_KEY,
});

async function getEncryptedData() {
  const response = await client.get("/secure-data");
  // response.data is automatically decrypted
  console.log(response.data);
}
```

### 3. Using VormiaQuery with Custom Headers

```js
import { useVrmQuery } from "vormiaqueryjs";

const { data } = useVrmQuery({
  endpoint: "/profile",
  method: "GET",
  headers: {
    "X-Requested-With": "VormiaQuery",
    "X-Custom-Token": "abc123",
  },
});
```

### 4. Error Handling for Validation Errors

```js
import { useVrmMutation } from "vormiaqueryjs";

function RegisterForm() {
  const mutation = useVrmMutation({ endpoint: "/register", method: "POST" });

  const handleRegister = async (formData) => {
    try {
      await mutation.mutateAsync(formData);
    } catch (error) {
      if (error.isValidationError()) {
        // Show validation errors to the user
        const errors = error.getValidationErrors();
        alert(JSON.stringify(errors));
      }
    }
  };

  // ...form rendering
}
```

### 5. VormiaQuery in a Next.js API Route (Node.js)

```js
// pages/api/proxy.js
import { VormiaClient } from "vormiaqueryjs";

const client = new VormiaClient({
  baseURL: "https://api.example.com",
  rsaEncrypt: true,
  privateKey: process.env.VORMIA_PRIVATE_KEY,
  publicKey: process.env.VORMIA_PUBLIC_KEY,
});

export default async function handler(req, res) {
  const apiRes = await client.get("/protected-data");
  res.status(200).json(apiRes.data);
}
```
