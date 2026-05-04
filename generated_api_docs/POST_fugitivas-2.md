# POST /fugitivas-2

Calcula e persiste emissões de fugitivas 2 no banco.

## Request body

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
id_gas | integer | yes | 
variacao_estoque | number | yes | 
quantidade_transferida | number | yes | 
mudanca_capacidade | number | yes | 
id_empresa | string | yes | 
id_unidade_negocio | string | yes | 
nvl_unidade_negocio | integer | yes | 
data | string | yes | 
descricao | string | yes | 
link_referencia | string | yes | 
cnpj_cliente | string | yes | 
ano | integer | yes | 
escopo | string | yes | 
id_usuario | string | yes | 

## Response

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
status | string | yes | 
message | string | yes | 
data | object | yes | 
data.resultado_calculo | object | yes | 
data.resultado_calculo.emissao_total | number | yes | 
data.resultado_calculo.fator_gwp | number | yes | 
data.resultado_calculo.nome_gwp | string | yes | 
data.ids_gerados | object | yes | 
data.ids_gerados.documento_id | string | yes | 
data.ids_gerados.fugitivas_2_id | string | yes | 
data.ids_gerados.emissao_id | string | yes | 
data.metadados | object | yes | 
data.metadados.empresa | string | yes | 
data.metadados.unidade | string | yes | 
data.metadados.gas | string | yes | 
data.metadados.data | string | yes | 
data.metadados.ano | integer | yes | 
data.metadados.escopo | string | yes | 

## TypeScript

```typescript
// Generated TypeScript interfaces
export type POSTFugitivas-2Request = { id_gas: number; variacao_estoque: number; quantidade_transferida: number; mudanca_capacidade: number; id_empresa: string; id_unidade_negocio: string; nvl_unidade_negocio: number; data: string; descricao: string; link_referencia: string; cnpj_cliente: string; ano: number; escopo: string; id_usuario: string };
export type POSTFugitivas-2Response = { status: string; message: string; data: { resultado_calculo: { emissao_total: number; fator_gwp: number; nome_gwp: string }; ids_gerados: { documento_id: string; fugitivas_2_id: string; emissao_id: string }; metadados: { empresa: string; unidade: string; gas: string; data: string; ano: number; escopo: string } } };
```