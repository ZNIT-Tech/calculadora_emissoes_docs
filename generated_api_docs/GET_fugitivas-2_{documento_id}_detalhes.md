# GET /fugitivas-2/{documento_id}/detalhes

Consulta detalhes específicos de um documento de fugitivas 2.

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
export type GETFugitivas-2{DocumentoId}DetalhesResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```