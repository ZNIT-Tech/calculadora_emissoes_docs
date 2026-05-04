# GET /api/v1/api-keys/admin/permissoes

Lista todos os tipos de permissão disponíveis

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
export type GETApiV1Api-KeysAdminPermissoesResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```