# POST /fugitivas-1/calcular

Calcula emissões de fugitivas 1 SEM persistir no banco.

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
export type POSTFugitivas-1CalcularResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```