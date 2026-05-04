# POST /combustao-estacionaria

Calcula e persiste emissões de combustão estacionária no banco.

## Request body

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
combustivel_id | integer | yes | 
setor_id | integer | yes | 
consumo | number | yes | 
unidade | string | yes | 
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
export type POSTCombustao-EstacionariaRequest = { combustivel_id: number; setor_id: number; consumo: number; unidade: string; data: string; id_empresa: string; id_unidade_negocio: string; nvl_unidade_negocio: number; id_usuario?: string; descricao?: string; cnpj_fornecedor?: string; link_referencia?: string };
export type POSTCombustao-EstacionariaResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```