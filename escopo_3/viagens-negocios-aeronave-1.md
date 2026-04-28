# **Viagens de Negócios — Aeronave (Distância entre aeroportos)**

Calcula emissões de voos entre aeroportos usando distância Haversine e fatores por faixa de distância.

---

## **Endpoint**

### **POST /api/v1/emissoes-aeroporto**

#### **Requisição (JSON)**
```json
{
  "id_aeroporto_saida": 123,
  "id_aeroporto_chegada": 456,
  "trechos": 2
}
```

#### **Campos Obrigatórios**
- `id_aeroporto_saida`, `id_aeroporto_chegada` (trechos é opcional, default=1).

## **Metodologia**
- Calcula distância entre coordenadas (Haversine).
- Seleciona fatores por faixa de distância (curta, média, longa).
- Aplica fator de rota indireta (+8%).
- Converte emissores para tCO2e usando os GWPs (CH4=28, N2O=265).

## **Resposta (exemplo)**
```json
{
  "status": "success",
  "data": {
    "distancia_km": 1200.0,
    "emissao_co2": 120.0,
    "emissao_ch4": 0.01,
    "emissao_n2o": 0.005,
    "emissao_total": 120.28,
    "trechos": 2
  }
}
```

Referência: `src/app/escopos/escopo_3/viagens_negocios_aeronave_1`
