# GET /api/v1/onboarding/admin/empresas/relatorio

Relatório detalhado de empresas com múltiplos filtros

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
export type GETApiV1OnboardingAdminEmpresasRelatorioResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```