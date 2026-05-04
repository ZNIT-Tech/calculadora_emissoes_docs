# POST /combustao-movel-1/calcular

Calcula emissões de combustão móvel 1 (veículos) SEM persistir no banco.

## Request body

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
id_veiculo | integer | yes | 
ano_veiculo | integer | yes | 
consumo_mensal | number | yes | 
data | string | yes | 
cnpj | string | yes | 
descricao | string | no | 

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
export type POSTCombustao-Movel-1CalcularRequest = { id_veiculo: number; ano_veiculo: number; consumo_mensal: number; data: string; cnpj: string; descricao?: string };
export type POSTCombustao-Movel-1CalcularResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```