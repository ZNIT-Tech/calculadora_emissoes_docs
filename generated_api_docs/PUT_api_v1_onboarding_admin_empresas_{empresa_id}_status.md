# PUT /api/v1/onboarding/admin/empresas/{empresa_id}/status

Ativa ou desativa uma empresa

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
export type PUTApiV1OnboardingAdminEmpresas{EmpresaId}StatusResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```