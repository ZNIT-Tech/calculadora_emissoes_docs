# GET /dashboard/emissoes-todos-escopos

Retorna total de emissões somando todos os escopos.

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
export type GETDashboardEmissoes-Todos-EscoposResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```