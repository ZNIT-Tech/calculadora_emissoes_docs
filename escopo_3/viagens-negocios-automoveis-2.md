# **Viagens de Negócios — Automóveis (2)**

Versão alternativa de cálculo para automóveis que trata combustíveis compostos, casos especiais (Mitre) e unidades (
`l`/`m3`).

---

## **Endpoint**

### **POST /viagens-negocios-automoveis-2**

#### **Requisição (JSON)**
```json
{
  "cnpj_usuario": "12345678901234",
  "id_combustivel": 1,
  "consumo_mensal": 100.0,
  "consumo_anual": 0.0,
  "data": "2024-06-15",
  "unidade": "L"
}
```

#### **Campos Obrigatórios**
- `cnpj_usuario`, `id_combustivel`, `consumo_mensal`/`consumo_anual`, `data`, `unidade`.

## **Metodologia**
- Trata casos especiais Mitre 2024 (valores fixos por mês).
- Para combustíveis colombianos, busca fatores compostos no Supabase.
- Para Brasil, calcula participação de biocombustível (etanol/biodiesel) pelo percentual do mês.
- Emissões calculadas por multiplicação do fator e do consumo (divisão por 1000 quando aplicável).

## **Resposta (exemplo)**
```json
{
  "status": "success",
  "data": {
    "emissao_co2": 230.0,
    "emissao_ch4": 0.05,
    "emissao_n2o": 0.02,
    "emissao_total": 231.9
  }
}
```

Referência de implementação: `src/app/escopos/escopo_3/viagens_negocios_automoveis_2`
