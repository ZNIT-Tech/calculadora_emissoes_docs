# POST /combustao-movel-2

Calcula e persiste emissões de combustão móvel 2 no banco.

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
export type POSTCombustao-Movel-2Response = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```