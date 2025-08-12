# Blogiz-Starter-Pack

## Installation:

1. Clone the repository.
2. Install dependencies using `npm install`.
3. Run the project using `npm run dev`.

---
.
├─ app
│  ├─ layout.tsx                # Wraps the app with Redux <Provider>
│  └─ page.tsx                  # Example route (can render a client component)
├─ src
│  ├─ actions
│  │  └─ createBlogs.ts         # Server Action to create a blog (POST /blogs)
│  ├─ api
│  │  └─ BaseApi.ts             # RTK Query base API (GET /blogs)
│  ├─ lib
│  │  └─ Provider.tsx           # Redux Provider (client component)
│  └─ redux
│     └─ store.ts               # Redux store with RTK Query middleware
├─ public
│  └─ favicon.ico
├─ .env.local.example           # Example envs (copy to .env.local)
├─ next.config.ts
├─ package.json
├─ tsconfig.json
└─ README.md

## src/redux/store.ts
```
import { configureStore } from '@reduxjs/toolkit';
import { api } from '../api/BaseApi';

export const store = configureStore({
  reducer: { [api.reducerPath]: api.reducer },
  middleware: (gDM) => gDM().concat(api.middleware),
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```


## src/api/BaseApi.ts
```
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';

export const api = createApi({
  reducerPath: 'api',
  baseQuery: fetchBaseQuery({
    baseUrl: process.env.NEXT_PUBLIC_API_URL || 'http://localhost:5000/',
  }),
  endpoints: (b) => ({
    getBlogs: b.query<any[], void>({ query: () => 'blogs' }),
  }),
});

export const { useGetBlogsQuery } = api;

```

## src/lib/Provider.tsx
```
'use client';
import { Provider } from 'react-redux';
import { store } from '../redux/store';

export default function AppProvider({ children }: { children: React.ReactNode }) {
  return <Provider store={store}>{children}</Provider>;
}

```

## src/actions/createBlogs.ts
```
'use server';

export async function createBlog(data: any) {
  const url = `${process.env.NEXT_PUBLIC_API_URL || 'http://localhost:5000'}/blogs`;

  const res = await fetch(url, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data),
    cache: 'no-store',
  });

  if (!res.ok) throw new Error('Failed to create blog');
  return res.json();
}

```
