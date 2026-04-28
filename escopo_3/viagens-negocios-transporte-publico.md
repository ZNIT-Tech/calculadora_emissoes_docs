# **Viagens de Negócios — Transporte Público**

Calcula emissões por transporte público com lógica específica para trem/metrô, balsas e ônibus.

---

## **Endpoint**

### **POST /api/v1/viagens-negocios-transporte-publico/**

#### **Requisição (JSON)**
```json
{
  "categoria": "Transporte Público",
  "id_veiculo": 10,
  "nro_passageiros": 100,
  "dist_percorrida": 20.0,
  "data": "2024-06-15"
}
```

#### **Campos Obrigatórios**
- `categoria`, `id_veiculo`, `nro_passageiros`, `dist_percorrida`, `data`.

## **Metodologia**
- Busca fatores do veículo no Supabase.
- Para `Trem`/`Metrô`: calcula emissões por passageiro diretamente com fator_co2.
- Para `Balsa`: aplica fator por passageiro e distância.
- Para `Ônibus`: considera mix fóssil + biodiesel (Yearly) e soma fósseis+bio para CH4/N2O/CO2.

Resposta inclui fatores utilizados, emissões por gás e `emissao_total`.

Referência: `src/app/escopos/escopo_3/viagens_negocios_transporte_publico`
