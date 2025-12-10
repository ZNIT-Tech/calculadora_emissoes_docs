# **Combustão Móvel 1**

Permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes da combustão móvel (veículos).

---

## **Cálculo de Emissões**

### **POST /api/combustao-movel-1**

Calcula as emissões de GEE para um veículo específico.

#### **Requisição:**
```json
{
  "id_veiculo": 12,
  "ano_veiculo": 2020,
  "consumo_mensal": 150.5,
  "consumo_anual": 1800.0,
  "data": "2024-06-15",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "nvl_unidade_negocio": 2
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_veiculo` | número | ID do veículo (consulte `/api/gwp` para lista) | 12 |
| `ano_veiculo` | número | Ano de fabricação do veículo | 2020 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-06-15" |
| `id_empresa` | string | ID da empresa | "dd68b58c-9155-42df..." |
| `id_unidade_negocio` | string | ID da unidade de negócio | "680a4fe4-d2f1-4f2d..." |
| `nvl_unidade_negocio` | número | Nível hierárquico (1-10) | 2 |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `consumo_mensal` | número | Consumo mensal em litros/m³ |
| `consumo_anual` | número | Consumo anual em litros/m³ |
| `descricao` | string | Descrição opcional do cálculo |
| `id_usuario` | string | ID do usuário que realizou o cálculo |

> **Nota:** Forneça `consumo_mensal` OU `consumo_anual`. Se ambos forem fornecidos, o mensal será usado.

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/combustao-movel-1" \
  -H "Content-Type: application/json" \
  -d '{
    "id_veiculo": 1,
    "ano_veiculo": 2020,
    "consumo_mensal": 150.5,
    "data": "2024-06-15",
    "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
    "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
    "nvl_unidade_negocio": 2
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "data": {
    "consumo_fossil": 120.4,
    "consumo_biocombustivel": 30.1,
    "emissao_co2": 3.25,
    "emissao_ch4": 0.005,
    "emissao_n2o": 0.002,
    "emissao_total": 3.876,
    "emissao_co2_biogenico": 0.45,
    "fator_co2": 0.027,
    "fator_ch4": 0.000033,
    "fator_n2o": 0.000011,
    "fator_co2_biogenico": 0.015,
    "biocombustivel_utilizado": "Etanol",
    "combustivel_fossil_utilizado": "Gasolina Automotiva (pura)",
    "veiculo_input": "Carro de Passeio"
  },
  "documento_id": "550e8400-e29b-41d4-a716-446655440000",
  "emissao_id": 123
}
```

#### **Códigos de Erro:**
- `400`: Campos obrigatórios ausentes ou inválidos
- `404`: Empresa ou unidade não encontrada
- `500`: Erro interno do servidor

---

## **Consulta de Emissões**

### **1. GET /api/combustao-movel-1/unidade**

Consulta emissões por unidade de negócio, incluindo subunidades.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `id_unidade` | ✓ | string | ID da unidade |
| `nivel` | ✓ | número | Nível hierárquico (1-10) |
| `data_inicio` | ✗ | string | Data inicial (YYYY-MM-DD) |
| `data_fim` | ✗ | string | Data final (YYYY-MM-DD) |
| `incluir_descendentes` | ✗ | boolean | Incluir unidades filhas (padrão: true) |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/combustao-movel-1/unidade?id_unidade=550e8400-e29b-41d4-a716-446655440000&nivel=2"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "unidade": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "nivel": 2,
      "nome": "São Paulo"
    },
    "emissao_total": 1500.75,
    "detalhes_por_unidade": [
      {
        "id": "550e8400-e29b-41d4-a716-446655440000",
        "nome": "São Paulo",
        "nivel": 2,
        "emissao_total": 500.25,
        "emissao_co2": 450.0,
        "emissao_ch4": 1.5,
        "emissao_n2o": 0.75
      }
    ]
  }
}
```

### **2. GET /api/combustao-movel-1/hierarquia-completa**

Consulta hierarquia completa de uma empresa.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `id_empresa` | ✓ | string | ID da empresa |
| `nivel_inicio` | ✗ | número | Nível inicial (padrão: 1) |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/combustao-movel-1/hierarquia-completa?id_empresa=123e4567-e89b-12d3-a456-426614174000"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "id_empresa": "123e4567-e89b-12d3-a456-426614174000",
    "emissao_total_empresa": 3250.5,
    "hierarquia": [
      {
        "id": "unidade-1-uuid",
        "nome": "Unidade Matriz",
        "nivel": 1,
        "emissao_direta": 1200.0,
        "emissao_total": 3250.5
      }
    ]
  }
}
```

### **3. GET /api/combustao-movel-1/detalhado**

Consulta detalhada agrupada por veículo.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `id_unidade` | ✓ | string | ID da unidade |
| `nivel` | ✓ | número | Nível hierárquico |
| `agrupar_por` | ✗ | string | "veiculo" ou "documento" (padrão: "veiculo") |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/combustao-movel-1/detalhado?id_unidade=550e8400-e29b-41d4-a716-446655440000&nivel=2&agrupar_por=veiculo"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": [
    {
      "veiculo": "Carro de Passeio",
      "total_documentos": 3,
      "total_consumo_anual": 4500.0,
      "unidades": ["uuid-sp-1", "uuid-sp-2"]
    }
  ],
  "total_registros": 1
}
```
---

## **Regras de Cálculo**

1. **Ano do veículo:** Valores abaixo de 2000 são automaticamente ajustados para 2000
2. **Consumo:** Use consumo mensal para cálculos mensais, anual para anuais
3. **Biocombustíveis:** Percentuais são ajustados automaticamente conforme mês e país
4. **Fatores de emissão:** São obtidos automaticamente do banco de dados

---

**Erros Comuns:**
1. `400` - Verifique se todos os campos obrigatórios foram enviados
2. `404` - Confirme que os IDs de empresa e unidade existem
3. `500` - Consulte os logs do servidor para detalhes

---
