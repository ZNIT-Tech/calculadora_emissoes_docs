# GET /mudanca-uso-solo/hierarquia-completa

Retorna a hierarquia completa de emissões de mudança no uso do solo para uma empresa.

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
export type GETMudanca-Uso-SoloHierarquia-CompletaResponse = { success: boolean; data: Record<string, any>; error: any; meta: Record<string, any> };
```