# GET /fugitivas-3/hierarquia-completa

Retorna a hierarquia completa de emissões de fugitivas 3 para uma empresa.

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
export type GETFugitivas-3Hierarquia-CompletaResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```