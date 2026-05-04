# POST /combustao-movel-2/calcular

Calcula emissões de combustão móvel 2 SEM persistir no banco.

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
export type POSTCombustao-Movel-2CalcularResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```