# **Viagens de Negócios — Automóveis (1)**

Calcula emissões de viagens de negócios para automóveis baseado no consumo informado e fatores por veículo/ano.

---

## **Endpoint**

### **POST /viagens-negocios-automoveis-1**

#### **Requisição (JSON)**
```json
{
  "id_veiculo": 5,
  "ano_veiculo": 2020,
  "consumo_mensal": 100.0,
  "consumo_anual": 0.0,
  "data": "2024-06-15"
}
```

#### **Campos Obrigatórios**
- `id_veiculo`, `ano_veiculo`, `consumo_mensal`/`consumo_anual`, `data`.

## **Metodologia**
- Seleciona consumo (mensal tem prioridade). 
- Obtém percentuais de biocombustível (etanol, biodiesel) pelo mês/ano.
- Busca fatores de emissão por veículo e ano no cadastro.
- Calcula consumo fóssil/biocombustível e aplica fatores (co2, ch4, n2o). 

$$\text{emissao\_total} = emissao\_co2 + emissao\_ch4 \times 28 + emissao\_n2o \times 265$$

## **Resposta (sucesso)**
```json
{
  "status": "success",
  "data": {
    "consumo_fossil": 90.0,
    "consumo_biocombustivel": 10.0,
    "emissao_co2": 50.0,
    "emissao_ch4": 0.02,
    "emissao_n2o": 0.01,
    "emissao_total": 50.56
  }
}
```

---

Referência de implementação: `src/app/escopos/escopo_3/viagens_negocios_automoveis_1`
