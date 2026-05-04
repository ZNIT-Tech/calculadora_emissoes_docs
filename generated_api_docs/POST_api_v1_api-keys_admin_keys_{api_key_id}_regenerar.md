# POST /api/v1/api-keys/admin/keys/{api_key_id}/regenerar

Regenera uma API Key (nova chave, mantém configurações)

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
export type POSTApiV1Api-KeysAdminKeys{ApiKeyId}RegenerarResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```