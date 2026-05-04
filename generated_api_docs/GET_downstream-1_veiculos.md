# GET /downstream-1/veiculos

Lista veículos disponíveis para downstream 1 (tabela lista_veiculos_combustiveis).

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
export type GETDownstream-1VeiculosResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```