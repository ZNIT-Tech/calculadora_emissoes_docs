# GET /health

Endpoint de health check para verificar se a API está rodando

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
export type GETHealthResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```