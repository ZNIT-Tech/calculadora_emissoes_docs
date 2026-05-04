# GET /combustao-estacionaria/hierarquia-completa

Retorna a hierarquia completa de emissões de combustão estacionária para uma empresa.

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
export type GETCombustao-EstacionariaHierarquia-CompletaResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```