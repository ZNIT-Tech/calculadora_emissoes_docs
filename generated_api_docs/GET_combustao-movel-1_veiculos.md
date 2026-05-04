# GET /combustao-movel-1/veiculos

Lista todos os veículos disponíveis para cálculo.

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
export type GETCombustao-Movel-1VeiculosResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```