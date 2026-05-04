# POST /combustao-movel-3/calcular

Calcula emissões de combustão móvel 3 SEM persistir no banco.

## Request body

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
tipo_veiculo_id | integer | yes | 
ano_veiculo | integer | yes | 
distancia_mensal | number | yes | 
data | string | yes | 
cnpj | string | no | 

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
export type POSTCombustao-Movel-3CalcularRequest = { tipo_veiculo_id: number; ano_veiculo: number; distancia_mensal: number; data: string; cnpj?: string };
export type POSTCombustao-Movel-3CalcularResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```