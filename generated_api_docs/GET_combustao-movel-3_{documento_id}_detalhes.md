# GET /combustao-movel-3/{documento_id}/detalhes

Consulta detalhes específicos de um documento de combustão móvel 3.

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
export type GETCombustao-Movel-3{DocumentoId}DetalhesResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```