# GET /combustao-movel-1/consolidado-veiculos

Gera um consolidado de emissões por tipo de veículo para uma empresa.

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
export type GETCombustao-Movel-1Consolidado-VeiculosResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```