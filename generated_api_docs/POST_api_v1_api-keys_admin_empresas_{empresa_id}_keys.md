# POST /api/v1/api-keys/admin/empresas/{empresa_id}/keys

Cria uma nova API Key para uma empresa (admin Zona Neutra)

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
export type POSTApiV1Api-KeysAdminEmpresas{EmpresaId}KeysResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```