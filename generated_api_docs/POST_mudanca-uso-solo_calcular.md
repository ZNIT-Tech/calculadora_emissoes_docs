# POST /mudanca-uso-solo/calcular

Calcula emissões de mudança no uso do solo SEM persistir no banco.

## Request body

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
id_estado | integer | yes | 
id_uso_anterior_solo | integer | yes | 
id_uso_posterior_solo | integer | no | 
id_bioma_anterior | integer | yes | 
area_mus | integer | yes | 
tipo_vegetacao_anterior | string | no | 
fitonomia_anterior | boolean | no | 
id_bioma_posterior | integer | no | 
dados_primarios_estoque_carbono | boolean | no | 
estoque_carbono_solo | number | no | 
estoque_carbono_biomassa | number | no | 
detalhamento_vegetacao | string | no | 
fator_personalizado | object | no | 

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
export type POSTMudanca-Uso-SoloCalcularRequest = { id_estado: number; id_uso_anterior_solo: number; id_uso_posterior_solo?: number; id_bioma_anterior: number; area_mus: number; tipo_vegetacao_anterior?: string; fitonomia_anterior?: boolean; id_bioma_posterior?: number; dados_primarios_estoque_carbono?: boolean; estoque_carbono_solo?: number; estoque_carbono_biomassa?: number; detalhamento_vegetacao?: string; fator_personalizado?: any };
export type POSTMudanca-Uso-SoloCalcularResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```