# GET /api/v1/onboarding/admin/empresas/{empresa_id}/unidades

Lista todas as unidades de uma empresa (admin Zona Neutra)

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
export type GETApiV1OnboardingAdminEmpresas{EmpresaId}UnidadesResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```