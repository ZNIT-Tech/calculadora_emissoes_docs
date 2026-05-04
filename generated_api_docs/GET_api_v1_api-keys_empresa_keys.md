# GET /api/v1/api-keys/empresa/keys

Lista API Keys da empresa do usuário autenticado

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
export type GETApiV1Api-KeysEmpresaKeysResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```