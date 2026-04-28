# **Downstream 3 (Transporte e Distribuição - Cargas Específicas) – Escopo 3**

Calculadora para emissões de GEE referentes ao transporte e distribuição a jusante de cargas com características específicas (refrigeradas, perigosas, a granel), quando o fator de emissão depende do tipo de carga.

---

## **Endpoints de Cálculo**

### **1. POST /api/v1/downstream-3**

Calcula emissões de GEE para uma operação de downstream 3 **SEM persistir no banco**.

#### **Requisição (payload JSON esperado pelo blueprint):**
```json
{
  "id_caminhao": 21,
  "consumo_anual": 36000.0,
  "carga_transportada": 2.5
}
```

#### **Parâmetros Obrigatórios (conforme `Downstream3Input`):**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_caminhao` | número | ID do caminhão na base de fatores | 21 |
| `consumo_anual` | número | Consumo anual total (L ou unidade equivalente) | 36000.0 |
| `carga_transportada` | número | Carga transportada (toneladas ou unidade equivalente) | 2.5 |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/v1/downstream-3" \
  -H "Content-Type: application/json" \
  -d '{
    "id_caminhao": 21,
    "consumo_anual": 36000.0,
    "carga_transportada": 2.5
  }'
```

#### **Resposta de Sucesso (exemplo com campos retornados pelo `Downstream3Output`):**
```json
{
  "status": "success",
  "data": {
    "fator_co2_diesel": 0.002,
    "fator_ch4_diesel": 0.000003,
    "fator_n2o_diesel": 0.000001,
    "emissao_total_co2": 180.0,
    "emissao_total_ch4": 0.27,
    "emissao_total_n2o": 0.09,
    "perc_biodiesel_anual": 0.05,
    "emissao_co2e": 180.0,
    "emissao_co2_biodiesel": 0.18
  }
}
```

#### **Observação:** atualmente o `calculator` do backend requer internamente um parâmetro `cnpj` em sua assinatura, mas o `blueprint` só valida e envia os campos acima — isso pode provocar erros em tempo de execução até que a integração seja ajustada.

---

### **2. POST /api/downstream-3**

Calcula e persiste emissões de GEE para downstream 3 **com persistência no banco**.

#### **Requisição:** (parâmetros de persistência idênticos aos dos outros downstream)
```json
{
  "id_veiculo": 21,
  "tipo_carga": "refrigerada",
  "consumo_mensal": 3000.0,
  "data": "2024-09-01",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "nvl_unidade_negocio": 2
}
```

#### **Resposta de Sucesso:** Similar ao `POST /api/downstream-3/calcular`, com `ids_gerados` e `metadados` de persistência.

---

## **Endpoints de Consulta**

- `GET /api/downstream-3/unidade` — consulta por unidade
- `GET /api/downstream-3/{documento_id}/detalhes` — detalhes do documento
- `GET /api/downstream-3/veiculos` — lista de veículos relevantes

---

## **Notas**
- Os fatores de emissão são obtidos da tabela `lista_veiculos_combustiveis` e podem variar por `tipo_carga`.
- Permissões e validações seguem o padrão `calcular:escopo3` e auditoria quando houver persistência.
