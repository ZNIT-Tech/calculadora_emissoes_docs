# POST /api/v1/onboarding/admin/empresas/{empresa_id}/unidades

Cria uma unidade de negócio (admin Zona Neutra)

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
export type POSTApiV1OnboardingAdminEmpresas{EmpresaId}UnidadesResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```