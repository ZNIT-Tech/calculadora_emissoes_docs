# GET /casa-trabalho-3/{documento_id}/detalhes

Retorna detalhes completos de um documento de casa trabalho 3.

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
export type GETCasa-Trabalho-3{DocumentoId}DetalhesResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```