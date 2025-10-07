# Request Library

The current project uses Alova as the only HTTP request library:

## Usage

- **Alova HTTP**: Path (src/http/request/alova.ts)
- **Example Code**: src/api/foo-alova.ts and src/api/foo.ts
- **API Documentation**: https://alova.js.org/

## Configuration Description

Alova instance is configured with:
- Automatic Token authentication and refresh
- Unified error handling and notifications
- Support for dynamic domain switching
- Built-in request/response interceptors

## Usage Examples

```typescript
import { http } from '@/http/request/alova'

// GET request
http.Get<ResponseType>('/api/path', {
  params: { id: 1 },
  headers: { 'Custom-Header': 'value' },
  meta: { toast: false } // Disable error notifications
})

// POST request  
http.Post<ResponseType>('/api/path', data, {
  params: { query: 'param' },
  headers: { 'Content-Type': 'application/json' }
})
