# GET /downstream-1/hierarquia-completa

Retorna hierarquia completa de emissões de downstream 1 para uma empresa.

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
export type GETDownstream-1Hierarquia-CompletaResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```