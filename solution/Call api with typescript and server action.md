Let's create a comprehensive example of a Call API server using Next.js 15 Server Actions, Refresh Tokens, Tag Unions, and DTOs with React 19. Here's a detailed implementation:

First, let's define our DTO and types:

```typescript
// lib/api/types.ts
export type ApiCallState =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: any }
  | { status: 'error'; error: string }
  | { status: 'unauthorized' };

export interface AuthTokens {
  accessToken: string;
  refreshToken: string;
  expiresAt: number;
}

export interface ApiResponse<T> {
  data?: T;
  error?: string;
  status: number;
}

// DTO for API request
export class UserDTO {
  constructor(
    public readonly id: string,
    public readonly email: string,
    public readonly name: string
  ) {}

  static fromJson(json: any): UserDTO {
    return new UserDTO(json.id, json.email, json.name);
  }
}
```

Now, let's create the Server Actions and API handler:

```typescript
// app/api/actions.ts
'use server';

import { cookies } from 'next/headers';
import { redirect } from 'next/navigation';
import { UserDTO, ApiCallState, AuthTokens } from '@/lib/api/types';

async function refreshAccessToken(refreshToken: string): Promise<AuthTokens> {
  const response = await fetch(`${process.env.API_URL}/auth/refresh`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${refreshToken}`,
    },
  });

  if (!response.ok) {
    throw new Error('Failed to refresh token');
  }

  return await response.json();
}

async function fetchWithAuth(url: string, options: RequestInit = {}): Promise<Response> {
  const cookieStore = cookies();
  const accessToken = cookieStore.get('access_token')?.value;
  const refreshToken = cookieStore.get('refresh_token')?.value;

  if (!accessToken || !refreshToken) {
    throw new Error('Unauthorized');
  }

  const headers = {
    ...options.headers,
    Authorization: `Bearer ${accessToken}`,
  };

  const response = await fetch(url, { ...options, headers });

  if (response.status === 401) {
    try {
      const newTokens = await refreshAccessToken(refreshToken);
      cookieStore.set('access_token', newTokens.accessToken, {
        expires: new Date(newTokens.expiresAt),
        httpOnly: true,
        secure: process.env.NODE_ENV === 'production',
      });
      
      // Retry with new token
      return await fetch(url, {
        ...options,
        headers: {
          ...options.headers,
          Authorization: `Bearer ${newTokens.accessToken}`,
        },
      });
    } catch (error) {
      cookieStore.delete('access_token');
      cookieStore.delete('refresh_token');
      throw new Error('Session expired');
    }
  }

  return response;
}

// Server Action to fetch user data
export async function getUserData(): Promise<ApiCallState> {
  try {
    const response = await fetchWithAuth(`${process.env.API_URL}/user`, {
      method: 'GET',
      cache: 'no-store',
    });

    if (!response.ok) {
      throw new Error('Failed to fetch user data');
    }

    const data = await response.json();
    const user = UserDTO.fromJson(data);

    return {
      status: 'success',
      data: user,
    };
  } catch (error: any) {
    if (error.message === 'Unauthorized' || error.message === 'Session expired') {
      return { status: 'unauthorized' };
    }
    return {
      status: 'error',
      error: error.message || 'Something went wrong',
    };
  }
}

// Server Action to logout
export async function logout() {
  const cookieStore = cookies();
  cookieStore.delete('access_token');
  cookieStore.delete('refresh_token');
  redirect('/login');
}
```

Now, let's create a React component using React 19 features:

```tsx
// app/components/UserProfile.tsx
'use client';

import { useState, useTransition } from 'react';
import { getUserData, logout } from '@/app/api/actions';
import { ApiCallState, UserDTO } from '@/lib/api/types';
import { useRouter } from 'next/navigation';

export default function UserProfile() {
  const [state, setState] = useState<ApiCallState>({ status: 'idle' });
  const [isPending, startTransition] = useTransition();
  const router = useRouter();

  const handleFetchUser = () => {
    startTransition(async () => {
      setState({ status: 'loading' });
      const result = await getUserData();

      switch (result.status) {
        case 'success':
          setState(result);
          break;
        case 'unauthorized':
          router.push('/login');
          break;
        case 'error':
          setState(result);
          break;
        default:
          setState(result);
      }
    });
  };

  const handleLogout = async () => {
    startTransition(async () => {
      await logout();
    });
  };

  return (
    <div className="p-4">
      <button
        onClick={handleFetchUser}
        disabled={isPending}
        className="px-4 py-2 bg-blue-500 text-white rounded disabled:opacity-50"
      >
        {isPending ? 'Loading...' : 'Fetch User Data'}
      </button>

      <button
        onClick={handleLogout}
        disabled={isPending}
        className="ml-2 px-4 py-2 bg-red-500 text-white rounded disabled:opacity-50"
      >
        Logout
      </button>

      {state.status === 'loading' && <p>Loading...</p>}
      {state.status === 'success' && state.data && (
        <div className="mt-4">
          <h2>User Profile</h2>
          <p>ID: {(state.data as UserDTO).id}</p>
          <p>Name: {(state.data as UserDTO).name}</p>
          <p>Email: {(state.data as UserDTO).email}</p>
        </div>
      )}
      {state.status === 'error' && (
        <p className="mt-2 text-red-500">Error: {state.error}</p>
      )}
    </div>
  );
}
```

And here's how to use it in a page:

```tsx
// app/profile/page.tsx
import UserProfile from '@/app/components/UserProfile';

export default function ProfilePage() {
  return (
    <div className="container mx-auto py-8">
      <h1 className="text-2xl font-bold mb-4">Profile</h1>
      <UserProfile />
    </div>
  );
}
```

Finally, add some middleware for authentication:

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';

export async function middleware(request: NextRequest) {
  const accessToken = request.cookies.get('access_token')?.value;
  const refreshToken = request.cookies.get('refresh_token')?.value;
  
  if (!accessToken || !refreshToken) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/profile/:path*', '/dashboard/:path*'],
};
```

This implementation includes:

1. **DTO**: `UserDTO` class for type-safe data handling
2. **Tag Unions**: `ApiCallState` using discriminated unions for state management
3. **Server Actions**: `getUserData` and `logout` actions running on the server
4. **Refresh Token**: Automatic token refresh logic with cookie management
5. **React 19**: Using `useTransition` for smooth state transitions
6. **Next.js 15**: Server Actions and latest routing features
7. **Authentication**: Middleware and cookie-based auth

Key features:
- Type-safe API calls with proper error handling
- Automatic token refresh when access token expires
- Optimistic updates with useTransition
- Secure cookie handling
- Protected routes with middleware
- Clean state management with tag unions

To use this, you'll need to:
1. Set up your environment variables (API_URL)
2. Configure your API endpoints
3. Add proper error boundaries
4. Style the components as needed

Make sure to install required dependencies:
```bash
npm install next@15 react@19 react-dom@19
```