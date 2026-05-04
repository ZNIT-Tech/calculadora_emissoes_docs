# POST /downstream-1

Calcula e persiste emissões de downstream 1 no banco.

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
export type POSTDownstream-1Response = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```