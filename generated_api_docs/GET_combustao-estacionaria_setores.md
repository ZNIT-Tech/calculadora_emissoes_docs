# GET /combustao-estacionaria/setores

Lista todos os setores disponíveis para combustão estacionária.

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
export type GETCombustao-EstacionariaSetoresResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```