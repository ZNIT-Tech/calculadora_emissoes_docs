# GET /api/v1/api-keys/admin/keys/{api_key_id}

Busca detalhes de uma API Key específica (admin Zona Neutra)

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
export type GETApiV1Api-KeysAdminKeys{ApiKeyId}Response = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```