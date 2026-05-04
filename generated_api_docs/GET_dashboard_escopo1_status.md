# GET /dashboard/escopo1/status

Retorna status e disponibilidade dos dados do dashboard.

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
export type GETDashboardEscopo1StatusResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```