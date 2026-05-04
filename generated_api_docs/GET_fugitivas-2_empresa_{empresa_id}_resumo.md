# GET /fugitivas-2/empresa/{empresa_id}/resumo

Consulta resumo de emissões de fugitivas 2 por empresa.

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
export type GETFugitivas-2Empresa{EmpresaId}ResumoResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```