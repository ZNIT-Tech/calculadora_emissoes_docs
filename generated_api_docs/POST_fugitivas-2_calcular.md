# POST /fugitivas-2/calcular

Calcula emissões de fugitivas 2 SEM persistir no banco.

## Request body

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
id_gas | integer | yes | 
variacao_estoque | number | yes | 
quantidade_transferida | number | yes | 
mudanca_capacidade | number | yes | 
data | string | yes | 
cnpj_cliente | string | yes | 
ano | integer | yes | 
escopo | string | yes | 

## Response

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
status | string | yes | 
message | string | yes | 
data | object | yes | 
data.emissao_total | number | yes | 
data.fator_gwp | number | yes | 
data.nome_gwp | string | yes | 
data.metadados | object | yes | 
data.metadados.gas | object | yes | 
data.metadados.gas.id | integer | yes | 
data.metadados.gas.nome | string | yes | 
data.metadados.parametros | object | yes | 
data.metadados.parametros.variacao_estoque | number | yes | 
data.metadados.parametros.quantidade_transferida | number | yes | 
data.metadados.parametros.mudanca_capacidade | number | yes | 
data.metadados.data_calculo | string | yes | 
data.metadados.ano | integer | yes | 
data.metadados.escopo | string | yes | 

## TypeScript

```typescript
// Generated TypeScript interfaces
export type POSTFugitivas-2CalcularRequest = { id_gas: number; variacao_estoque: number; quantidade_transferida: number; mudanca_capacidade: number; data: string; cnpj_cliente: string; ano: number; escopo: string };
export type POSTFugitivas-2CalcularResponse = { status: string; message: string; data: { emissao_total: number; fator_gwp: number; nome_gwp: string; metadados: { gas: { id: number; nome: string }; parametros: { variacao_estoque: number; quantidade_transferida: number; mudanca_capacidade: number }; data_calculo: string; ano: number; escopo: string } } };
```