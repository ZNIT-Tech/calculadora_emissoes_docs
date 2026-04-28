# **Downstream 4 (Transporte e Distribuição - Última Milha) – Escopo 3**

Calcula emissões de GEE para operações de última milha (entregas urbanas e micro-distribuição), onde fatores e perfis operacionais podem diferir significativamente.

---

## **Endpoints de Cálculo**

### **1. POST /downstream-4**

Calcula emissões de GEE para uma operação de downstream 4 **SEM persistir no banco**.

#### **Requisição (payload esperado pelo blueprint):**
```json
{
  "id_veiculo": 31,
  "ano_veiculo": 2021,
  "distancia_mensal": 1200.0,
  "distancia_anual": 0,
  "data": "2024-10-05"
}
```

#### **Parâmetros Obrigatórios (conforme implementação do blueprint):**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_veiculo` | número | ID do tipo de veículo | 31 |
| `ano_veiculo` | número | Ano do veículo | 2021 |
| `distancia_mensal` | número | Distância total mensal (km) | 1200.0 |
| `distancia_anual` | número | Distância anual (km), opcional se mensal preenchido | 0 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-10-05" |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/downstream-4" \
  -H "Content-Type: application/json" \
  -d '{
    "id_veiculo": 31,
    "ano_veiculo": 2021,
    "distancia_mensal": 1200.0,
    "data": "2024-10-05"
  }'
```

#### **Resposta de Sucesso (exemplo):**
```json
{
  "status": "success",
  "data": {
    "emissao_co2": 2.1,
    "emissao_ch4": 0.0025,
    "emissao_n2o": 0.0008,
    "emissao_total": 2.2578
  }
}
```

#### **Observação:** o `calculator` de backend atualmente exige um argumento `cnpj` em sua assinatura; o `blueprint` não envia `cnpj`, portanto pode haver inconsistências em tempo de execução até que seja corrigido.

---

### **2. POST /api/downstream-4**

Calcula e persiste emissões de GEE para downstream 4 **com persistência no banco**. Parâmetros de persistência seguem o padrão dos outros endpoints downstream.

---

## **Endpoints de Consulta**

- `GET /api/downstream-4/unidade`
- `GET /api/downstream-4/{documento_id}/detalhes`
- `GET /api/downstream-4/veiculos`

---

## **Notas**
- Fatores de emissão para operações urbanas podem usar perfis horários e ajustes por tipo de rota.
- Requer permissões `calcular:escopo3` para persistência.
