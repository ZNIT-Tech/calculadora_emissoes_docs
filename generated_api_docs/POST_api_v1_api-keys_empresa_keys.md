# POST /api/v1/api-keys/empresa/keys

Cria uma nova API Key para a própria empresa

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
export type POSTApiV1Api-KeysEmpresaKeysResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```