# GET /dashboard/escopo1/emissoes-totais

Retorna emissões totais do Escopo 1 em tCO2e.

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
export type GETDashboardEscopo1Emissoes-TotaisResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```