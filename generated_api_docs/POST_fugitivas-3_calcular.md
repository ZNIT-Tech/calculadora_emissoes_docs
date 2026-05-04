# POST /fugitivas-3/calcular

Calcula emissões de fugitivas 3 SEM persistir no banco.

## Request body

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
id_gas | integer | yes | 
id_refrigerador | integer | yes | 
carga_unidades_novas | number | yes | 
capacidade_unidades_operacao | number | yes | 
capacidade_unidades_dispensadas | number | yes | 
data | string | yes | 
cnpj | string | yes | 

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
export type POSTFugitivas-3CalcularRequest = { id_gas: number; id_refrigerador: number; carga_unidades_novas: number; capacidade_unidades_operacao: number; capacidade_unidades_dispensadas: number; data: string; cnpj: string };
export type POSTFugitivas-3CalcularResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```