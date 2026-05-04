# POST /fugitivas-3

Calcula e persiste emissões de fugitivas 3 no banco.

## Request body

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
id_gas | integer | yes | 
id_refrigerador | integer | yes | 
carga_unidades_novas | number | yes | 
capacidade_unidades_operacao | number | yes | 
capacidade_unidades_dispensadas | number | yes | 
data | string | yes | 
id_empresa | string | yes | 
id_unidade_negocio | string | yes | 
nvl_unidade_negocio | integer | yes | 
id_usuario | string | no | 
descricao | string | no | 
link_referencia | string | no | 

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
export type POSTFugitivas-3Request = { id_gas: number; id_refrigerador: number; carga_unidades_novas: number; capacidade_unidades_operacao: number; capacidade_unidades_dispensadas: number; data: string; id_empresa: string; id_unidade_negocio: string; nvl_unidade_negocio: number; id_usuario?: string; descricao?: string; link_referencia?: string };
export type POSTFugitivas-3Response = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```