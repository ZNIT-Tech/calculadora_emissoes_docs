# POST /combustao-estacionaria/calcular

Calcula emissões de combustão estacionária SEM persistir no banco.

## Request body

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
combustivel_id | integer | yes | 
setor_id | integer | yes | 
consumo | number | yes | 
unidade | string | yes | 
data | string | yes | 
id_empresa | string | no | 
id_unidade_negocio | string | no | 

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
export type POSTCombustao-EstacionariaCalcularRequest = { combustivel_id: number; setor_id: number; consumo: number; unidade: string; data: string; id_empresa?: string; id_unidade_negocio?: string };
export type POSTCombustao-EstacionariaCalcularResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```