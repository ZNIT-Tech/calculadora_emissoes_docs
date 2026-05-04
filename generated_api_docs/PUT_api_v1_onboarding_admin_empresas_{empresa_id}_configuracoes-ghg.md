# PUT /api/v1/onboarding/admin/empresas/{empresa_id}/configuracoes-ghg

Atualiza configurações de GHG e anos auditados

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
export type PUTApiV1OnboardingAdminEmpresas{EmpresaId}Configuracoes-GhgResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```