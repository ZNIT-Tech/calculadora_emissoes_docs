# GET /combustao-estacionaria/{documento_id}/detalhes

Consulta detalhes específicos de um documento de combustão estacionária.

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
export type GETCombustao-Estacionaria{DocumentoId}DetalhesResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```