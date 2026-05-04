# GET /api/v1/onboarding/admin/empresas/{empresa_id}

Busca empresa com todas as informações (admin Zona Neutra)

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
export type GETApiV1OnboardingAdminEmpresas{EmpresaId}Response = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```