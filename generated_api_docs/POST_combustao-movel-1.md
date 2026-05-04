# POST /combustao-movel-1

Calcula e persiste emissões de combustão móvel 1 (veículos) no banco.

## Request body

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
id_veiculo | integer | yes | 
ano_veiculo | integer | yes | 
consumo_mensal | number | yes | 
data | string | yes | 
id_empresa | string | yes | 
id_unidade_negocio | string | yes | 
nvl_unidade_negocio | integer | yes | 
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
export type POSTCombustao-Movel-1Request = { id_veiculo: number; ano_veiculo: number; consumo_mensal: number; data: string; id_empresa: string; id_unidade_negocio: string; nvl_unidade_negocio: number; descricao?: string; cnpj_fornecedor?: string; link_referencia?: string };
export type POSTCombustao-Movel-1Response = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```