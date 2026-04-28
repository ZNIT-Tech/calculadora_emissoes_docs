# **Viagens de Negócios — Aeronave (Consumo mensal)**

Calcula emissões para aviação a partir do consumo mensal informado, usando fatores por categoria de distância e acréscimos específicos da aviação.

---

## **Endpoint**

### **POST /api/v1/aeronave-quilometragem**

#### **Requisição (JSON)**
```json
{
  "dist_percorrida": 1500.0,
  "consumo_mensal": 2000.0
}
```

#### **Campos Obrigatórios**
- `dist_percorrida`, `consumo_mensal`.

## **Metodologia**
- Classifica a distância como curta/ média/ longa e obtém fatores e acréscimo percentual do banco.
- Calcula emissões multiplicando consumo pelos fatores e aplicando o multiplicador.

Resposta contém `emissao_co2`, `emissao_ch4`, `emissao_n2o`, `emissao_total`.

Referência: `src/app/escopos/escopo_3/viagens_negocios_aeronave_2`
