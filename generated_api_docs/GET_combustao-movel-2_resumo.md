# GET /combustao-movel-2/resumo

Retorna resumo estatístico das emissões de combustão móvel 2.

## Response

Campo | Tipo | Obrigatório | Descrição
--- | --- | --- | ---
status | string | yes | 
data | object | yes | 
data.ano | integer | yes | 
data.total_documentos | integer | yes | 
data.total_unidades | integer | yes | 
data.emissao_total | number | yes | 
data.media_emissao | number | yes | 
data.min_emissao | number | yes | 
data.max_emissao | number | yes | 
data.emissao_co2 | number | yes | 
data.emissao_ch4 | number | yes | 
data.emissao_n2o | number | yes | 

## TypeScript

```typescript
// Generated TypeScript interfaces
export type GETCombustao-Movel-2ResumoResponse = { status: string; data: { ano: number; total_documentos: number; total_unidades: number; emissao_total: number; media_emissao: number; min_emissao: number; max_emissao: number; emissao_co2: number; emissao_ch4: number; emissao_n2o: number } };
```