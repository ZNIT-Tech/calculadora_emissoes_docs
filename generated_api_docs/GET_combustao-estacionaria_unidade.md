# GET /combustao-estacionaria/unidade

Consulta emissões de combustão estacionária por unidade de negócio.

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
export type GETCombustao-EstacionariaUnidadeResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```