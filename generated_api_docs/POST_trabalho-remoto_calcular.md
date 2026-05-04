# POST /trabalho-remoto/calcular

Endpoint para cálculo de emissões de trabalho remoto.

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
export type POSTTrabalho-RemotoCalcularResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```