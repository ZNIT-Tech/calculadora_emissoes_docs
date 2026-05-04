# POST /api/residuos-ipcc

Endpoint para cálculo de emissões de resíduos segundo IPCC.

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
export type POSTApiResiduos-IpccResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```