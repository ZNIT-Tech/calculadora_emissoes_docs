# POST /efluentes/calcular

calcular_efluentes_sem_persistencia

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
export type POSTEfluentesCalcularResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```