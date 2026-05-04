# GET /api/v1/openapi.yaml

openapi_yaml

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
export type GETApiV1Openapi.YamlResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```