# POST /combustao-movel-3

Calcula e persiste emissões de combustão móvel 3 no banco.

## Request body

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
tipo_veiculo_id | integer | yes | 
ano_veiculo | integer | yes | 
distancia_mensal | number | yes | 
distancia_anual | integer | yes | 
data | string | yes | 
id_empresa | string | yes | 
id_unidade_negocio | string | yes | 
nvl_unidade_negocio | integer | yes | 
id_usuario | string | no | 
descricao | string | no | 
cnpj_fornecedor | string | no | 
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
export type POSTCombustao-Movel-3Request = { tipo_veiculo_id: number; ano_veiculo: number; distancia_mensal: number; distancia_anual: number; data: string; id_empresa: string; id_unidade_negocio: string; nvl_unidade_negocio: number; id_usuario?: string; descricao?: string; cnpj_fornecedor?: string; link_referencia?: string };
export type POSTCombustao-Movel-3Response = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```