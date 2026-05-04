# GET /combustao-estacionaria/combustiveis

Lista todos os combustíveis disponíveis para combustão estacionária.

## Response

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
success | boolean | yes | 
data | object | yes | 
error | object | yes | 
meta | object | yes | 

## TypeScript

```typescript
// Generated TypeScript interfaces
export type GETCombustao-EstacionariaCombustiveisResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```