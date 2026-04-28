# **Viagens de Negócios — Automóveis (3) (distância → consumo)**

Calcula emissões a partir de distâncias (km) convertidas para consumo usando consumo médio do veículo.

---

## **Endpoint**

### **POST /viagens-negocios-automoveis-3**

#### **Requisição (JSON)**
```json
{
  "id_veiculo": 5,
  "ano_veiculo": 2020,
  "distancia_mensal": 2000.0,
  "distancia_anual": 0.0,
  "data": "2024-06-15"
}
```

#### **Campos Obrigatórios**
- `id_veiculo`, `ano_veiculo`, `distancia_mensal`/`distancia_anual`, `data`.

## **Metodologia**
- Converte distância em consumo usando consumo médio (km/L) obtido do Supabase.
- Calcula consumo fóssil/biocombustível conforme percentuais do SIN.
- Aplica fatores de emissão por combustível e soma para CO2e.

Resposta: similar aos outros endpoints de automóveis — inclui `consumo_fossil`, `consumo_biocombustivel`, `emissao_co2`, `emissao_ch4`, `emissao_n2o` e `emissao_total`.

Referência: `src/app/escopos/escopo_3/viagens_negocios_automoveis_3`
