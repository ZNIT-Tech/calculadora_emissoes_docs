# GET /dashboard/escopo1/performance

Retorna métricas de performance do Escopo 1.

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
export type GETDashboardEscopo1PerformanceResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```