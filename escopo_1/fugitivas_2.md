# **Fugitivas 2**

Permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes de **emissões fugitivas** associadas a **equipamentos elétricos** (SF₆, PFCs, etc.) e outros gases que seguem a metodologia de **balanço de massa simplificado** baseado em variação de estoque, transferências e mudança de capacidade. Módulo versão 2, complementar ao Fugitivas 1 (refrigeração).

---

## **Endpoints de Cálculo**

### **1. POST /api/fugitivas-2/calcular**

Calcula emissões de GEE para uma operação com gases **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "id_gas": 5,
  "variacao_estoque": 100.5,
  "quantidade_transferida": 50.2,
  "mudanca_capacidade": 20.0,
  "data": "2024-07-20",
  "cnpj_cliente": "00.000.000/0001-91"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_gas` | número | ID do gás (consulte `/gases`) | 5 |
| `variacao_estoque` | número | Variação no estoque do gás (kg) | 100.5 |
| `quantidade_transferida` | número | Quantidade transferida para terceiros (kg) | 50.2 |
| `mudanca_capacidade` | número | Mudança na capacidade nominal de equipamentos (kg) | 20.0 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-20" |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `cnpj_cliente` | string | CNPJ do cliente (para obtenção da versão GHG) |
| `ano` | número | Ano de referência (padrão = ano da `data`) |
| `escopo` | string | Escopo da emissão (padrão: `"escopo1"`) |
| `id_empresa` | string | UUID da empresa (para metadados, não persiste) |
| `id_unidade_negocio` | string | UUID da unidade de negócio (para metadados, não persiste) |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/fugitivas-2/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_gas": 5,
    "variacao_estoque": 100.5,
    "quantidade_transferida": 50.2,
    "mudanca_capacidade": 20.0,
    "data": "2024-07-20",
    "cnpj_cliente": "00.000.000/0001-91"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões realizado com sucesso",
  "data": {
    "emissao_total": 1.705,
    "fator_gwp": 1.0,
    "nome_gwp": "CO2",
    "metadados": {
      "gas": {
        "id": 1,
        "nome": "CO2"
      },
      "parametros": {
        "variacao_estoque": 100.5,
        "quantidade_transferida": 50.2,
        "mudanca_capacidade": 20.0
      },
      "data_calculo": "2024-07-20",
      "ano": 2024,
      "escopo": "escopo1"
    }
  }
}
```

#### **Notas sobre o Cálculo:**
A emissão total (em kgCO₂e) é calculada como:

```
emissão_total (tCO₂e) = (variação_estoque + quantidade_transferida + mudança_capacidade) × GWP ÷ 1000
```

O **GWP** (Potencial de Aquecimento Global) é obtido dinamicamente a partir da tabela de gases, considerando a **versão GHG** vinculada ao CNPJ do cliente. Quando o CNPJ não é fornecido, utiliza‑se um valor padrão.

---

### **2. POST /api/fugitivas-2**

Calcula e persiste emissões de GEE para uma operação com gases **com persistência no banco** (cria documento, registro de fugitivas e emissões).

#### **Requisição:**
```json
{
  "id_gas": 5,
  "variacao_estoque": 100.5,
  "quantidade_transferida": 50.2,
  "mudanca_capacidade": 20.0,
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "nvl_unidade_negocio": 2,
  "data": "2024-07-20",
  "descricao": "Manutenção de disjuntores SF6",
  "link_referencia": "http://exemplo.com/laudo",
  "cnpj_cliente": "00.000.000/0001-91",
  "ano": 2024,
  "escopo": "escopo1",
  "id_usuario": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_gas` | número | ID do gás | 5 |
| `variacao_estoque` | número | Variação no estoque (kg) | 100.5 |
| `quantidade_transferida` | número | Quantidade transferida (kg) | 50.2 |
| `mudanca_capacidade` | número | Mudança na capacidade (kg) | 20.0 |
| `id_empresa` | string | UUID da empresa | "dd68b58c-9155..." |
| `id_unidade_negocio` | string | UUID da unidade de negócio | "680a4fe4-d2f1..." |
| `nvl_unidade_negocio` | número | Nível hierárquico (1–10) | 2 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-20" |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_usuario` | string | UUID do usuário responsável |
| `descricao` | string | Descrição adicional |
| `link_referencia` | string | Link para documento comprobatório |
| `cnpj_cliente` | string | CNPJ do cliente (para versão GHG) |
| `ano` | número | Ano de referência (padrão = ano da `data`) |
| `escopo` | string | Escopo da emissão (padrão: `"escopo1"`) |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/fugitivas-2" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_gas": 5,
    "variacao_estoque": 100.5,
    "quantidade_transferida": 50.2,
    "mudanca_capacidade": 20.0,
    "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
    "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
    "nvl_unidade_negocio": 2,
    "data": "2024-07-20"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões de fugitivas 2 realizado com sucesso",
  "data": {
    "resultado_calculo": {
      "emissao_total": 1.705,
      "fator_gwp": 1.0,
      "nome_gwp": "CO2"
    },
    "ids_gerados": {
      "documento_id": "550e8400-e29b-41d4-a716-446655440000",
      "fugitivas_2_id": 123,
      "emissao_id": 456
    },
    "metadados": {
      "empresa": "Empresa Exemplo",
      "unidade": "Filial São Paulo",
      "gas": "CO2",
      "data": "2024-07-20",
      "ano": 2024,
      "escopo": "escopo1"
    }
  }
}
```

#### **Notas Importantes:**
- Endpoint submete à **validação de auditoria** (`@require_audit_validation`).
- A categoria do documento é obtida dinamicamente via `db.get_categoria_fugitivas_2_id()` (fallback = **7**).
- A empresa e a unidade devem existir e estar ativas.
- O CNPJ do cliente (ou da empresa) é utilizado para obter a versão GHG e o GWP adequado para o gás.
- Os parâmetros de entrada são persistidos na tabela **`cat_fugitivas_2`**.
- As emissões são distribuídas automaticamente entre `emissao_co2`, `emissao_ch4`, `emissao_n2o` conforme o `id_gas` (1 = CO₂, 2 = CH₄, 3 = N₂O). Demais gases têm emissão total registrada sem discriminação.

---

## **Endpoints de Consulta**

### **1. GET /api/fugitivas-2/unidade**

Consulta emissões de fugitivas 2 agregadas por unidade de negócio, com possibilidade de incluir unidades descendentes.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_unidade` | ✓ | string | UUID da unidade de negócio | — |
| `nivel` | ✓ | número | Nível hierárquico da unidade (1–10) | — |
| `data_inicio` | ✗ | string | Data inicial (YYYY-MM-DD) | — |
| `data_fim` | ✗ | string | Data final (YYYY-MM-DD) | — |
| `incluir_descendentes` | ✗ | boolean | Incluir unidades filhas | `true` |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/fugitivas-2/unidade?id_unidade=680a4fe4-d2f1-4f2d-ab99-556effb557c9&nivel=2&incluir_descendentes=true" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "unidade": {
      "id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
      "nome": "Filial São Paulo",
      "nivel": 2
    },
    "emissao_total": 1150.0,
    "quantidade_documentos": 1,
    "quantidade_unidades": 1,
    "detalhes": [
      {
        "documento_id": "550e8400-e29b-41d4-a716-446655440000",
        "data": "2024-07-20",
        "descricao": "Fugitivas 2 - Gás: CO2",
        "unidade": {
          "id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
          "nome": "Filial São Paulo",
          "nivel": 2
        },
        "emissoes": {
          "co2": 1.705,
          "ch4": 0,
          "n2o": 0,
          "total": 1.705
        },
        "created_at": "2024-07-20T14:30:00Z"
      }
    ],
    "filtros": {
      "data_inicio": null,
      "data_fim": null,
      "incluir_descendentes": true
    }
  }
}
```

---

### **2. GET /api/fugitivas-2/{documento_id}/detalhes**

Consulta detalhes completos de um documento específico de fugitivas 2.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path parameter) |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/fugitivas-2/550e8400-e29b-41d4-a716-446655440000/detalhes" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "documento": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
      "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
      "nvl_unidade_negocio": 2,
      "data": "2024-07-20",
      "descricao": "Fugitivas 2 - Gás: CO2",
      "link_referencia": null,
      "id_categoria": 7,
      "id_usuario": null,
      "created_at": "2024-07-20T14:30:00Z"
    },
    "fugitivas_2": {
      "id": 123,
      "id_gas": 1,
      "variacao_estoque": 100.5,
      "quantidade_transferida": 50.2,
      "mudanca_capacidade": 20.0,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-20T14:30:00Z"
    },
    "gas": {
      "id": 1,
      "gas": "CO2",
      "gwp": 1,
      "familia_top": "Dióxido de Carbono"
    },
    "emissoes": {
      "id": 456,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "emissao_co2": 1.705,
      "emissao_ch4": 0,
      "emissao_n2o": 0,
      "emissao_total": 1.705,
      "emissao_biogenica": 0,
      "created_at": "2024-07-20T14:30:00Z"
    }
  }
}
```

---

### **3. GET /api/fugitivas-2/gases**

Lista todos os gases disponíveis para cálculo de fugitivas 2, com seus respectivos GWP e família.

#### **Parâmetros:**
Nenhum.

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/fugitivas-2/gases" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "gas": "CO₂",
      "familia_top": "Dióxido de Carbono",
      "gwp": 1,
      "referencia": "IPCC AR5"
    },
    {
      "id": 2,
      "gas": "CH₄",
      "familia_top": "Metano",
      "gwp": 28,
      "referencia": "IPCC AR5"
    },
    {
      "id": 3,
      "gas": "N₂O",
      "familia_top": "Óxido Nitroso",
      "gwp": 265,
      "referencia": "IPCC AR5"
    },
    {
      "id": 5,
      "gas": "SF₆",
      "familia_top": "Hexafluoreto de Enxofre",
      "gwp": 23500,
      "referencia": "IPCC AR5"
    }
  ],
  "total": 4
}
```

---

### **4. GET /api/fugitivas-2/empresa/<empresa_id>/resumo**

Retorna um resumo estatístico das emissões de fugitivas 2 para uma empresa, com distribuição por gás e por mês (quando ano é informado).

#### **Parâmetros de Path:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `empresa_id` | ✓ | string | UUID da empresa |

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `ano` | ✗ | string | Ano para filtrar (formato YYYY) | — |
| `nivel_agregacao` | ✗ | número | Nível hierárquico para contagem de unidades | 1 |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/fugitivas-2/empresa/dd68b58c-9155-42df-97ba-8ec86953c360/resumo?ano=2024" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "empresa": {
      "id": "dd68b58c-9155-42df-97ba-8ec86953c360",
      "nome": "Empresa Exemplo",
      "cnpj": "00.000.000/0000-00"
    },
    "emissao_total": 5280.35,
    "quantidade_documentos": 25,
    "quantidade_unidades": 10,
    "distribuicao_gases": {
      "co2": 1200.5,
      "ch4": 80.25,
      "n2o": 0
    },
    "gases_utilizados": [
      {
        "id": 1,
        "nome": "CO₂",
        "quantidade": 15
      },
      {
        "id": 5,
        "nome": "SF₆",
        "quantidade": 10
      }
    ],
    "por_mes": [
      {
        "mes": 1,
        "nome_mes": "January",
        "emissao": 450.2
      },
      {
        "mes": 2,
        "nome_mes": "February",
        "emissao": 380.1
      }
    ],
    "periodo": {
      "ano": "2024",
      "filtro_aplicado": true
    }
  }
}
```

---

### **5. GET /api/fugitivas-2/exportar** (Em desenvolvimento)

> **Nota:** Este endpoint está em fase de implementação. No momento retorna erro 501.

Exporta emissões de fugitivas 2 para formato CSV (futuramente também Excel).

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `id_empresa` | ✓ | string | UUID da empresa |
| `data_inicio` | ✗ | string | Data inicial (YYYY-MM-DD) |
| `data_fim` | ✗ | string | Data final (YYYY-MM-DD) |
| `formato` | ✗ | string | `"csv"` ou `"excel"` (padrão: `"csv"`) |

---

## **Autenticação e Permissões**

Todos os endpoints exigem **API Key** no header `X-API-Key` e a permissão adequada.

| Endpoint | Método | Permissão Requerida |
|----------|--------|---------------------|
| `/fugitivas-2/calcular` | POST | `calcular:escopo1` |
| `/fugitivas-2` (persistência) | POST | `calcular:escopo1` |
| `/fugitivas-2/unidade` | GET | `consultar:resultados` |
| `/fugitivas-2/{documento_id}/detalhes` | GET | `consultar:resultados` |
| `/fugitivas-2/gases` | GET | `consultar:resultados` |
| `/fugitivas-2/empresa/<empresa_id>/resumo` | GET | `consultar:resultados` |
| `/fugitivas-2/exportar` | GET | `consultar:resultados` |

**Header de Autenticação:**
```http
X-API-Key: sua-chave-api-aqui
```

---

## **Regras de Cálculo Atualizadas**

### **Metodologia**
O cálculo segue um **balanço de massa simplificado** para gases utilizados em equipamentos elétricos (ex.: SF₆ em disjuntores) ou outros processos onde o gás é estocado, transferido ou tem sua capacidade nominal alterada:

```
Emissão (kg do gás) = variação_estoque + quantidade_transferida + mudança_capacidade
```

O resultado em kg do gás é convertido para **tCO₂e** multiplicando pelo **Potencial de Aquecimento Global (GWP)** do gás específico e dividindo por 1000.

### **Versão GHG e GWP**
- O GWP de cada gás é armazenado na tabela `lista_gwp` e pode variar conforme a **versão GHG** adotada.
- A versão GHG é obtida a partir do **CNPJ do cliente** informado (ou, no fluxo persistente, do CNPJ da empresa).
- Caso o CNPJ não seja fornecido ou a versão GHG não seja encontrada, o sistema utiliza um valor padrão (ex.: versão mais recente).

### **Classificação das Emissões**
- Quando o `id_gas` é **1 (CO₂)** , a emissão total é alocada no campo `emissao_co2`.
- Quando o `id_gas` é **2 (CH₄)** , a emissão total é alocada no campo `emissao_ch4`.
- Quando o `id_gas` é **3 (N₂O)** , a emissão total é alocada no campo `emissao_n2o`.
- Para **demais gases** (ex.: SF₆, PFCs, HFCs), a emissão total é registrada apenas no campo `emissao_total`; os campos específicos permanecem zerados.

### **Validações:**
- Todos os valores (variação, transferência, capacidade) devem ser **não negativos**.
- O nível da unidade (`nvl_unidade_negocio`) deve estar entre 1 e 10.
- A empresa e a unidade devem existir no banco (fluxo persistente).
- A data deve estar no formato ISO (`YYYY-MM-DD`).

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Campos obrigatórios ausentes ou inválidos | Verifique o corpo da requisição |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Verifique suas permissões de usuário |
| `404` | Recurso não encontrado | Verifique IDs de empresa, unidade, gás |
| `500` | Erro interno do servidor | Consulte os logs do sistema |
| `501` | Funcionalidade não implementada | Endpoint de exportação Excel ainda não disponível |

---

## **Exemplos de Erro Comuns**

### **Valores negativos:**
```json
{
  "error": "Valores não podem ser negativos"
}
```

### **Gás não encontrado:**
```json
{
  "error": "Gás ID 99 não encontrado"
}
```

### **Empresa não encontrada:**
```json
{
  "error": "Empresa não encontrada"
}
```

### **Unidade não encontrada:**
```json
{
  "error": "Unidade de negócio não encontrada"
}
```

### **Nível da unidade fora do intervalo:**
```json
{
  "error": "nvl_unidade_negocio deve estar entre 1 e 10"
}
```

### **CNPJ inválido ou sem versão GHG:**
```json
{
  "error": "Não foi possível obter a versão GHG para o CNPJ: 00.000.000/0001-91"
}
```

---

## **Observações Importantes**

1. **Dois modos de cálculo:** Utilize `/calcular` para testes e simulações; utilize o POST sem `/calcular` para lançamento oficial com persistência e auditoria.
2. **Dependência de versão GHG:** Os fatores de GWP são obtidos dinamicamente com base no CNPJ do cliente (ou da empresa). É recomendável sempre informar o `cnpj_cliente` para garantir o uso da versão correta.
3. **Parâmetros específicos:** Diferentemente do Fugitivas 1, este módulo utiliza **variação de estoque**, **quantidade transferida** e **mudança de capacidade** – adequado para gases em equipamentos elétricos, tanques ou processos industriais.
4. **Categoria:** A categoria de Fugitivas 2 é obtida dinamicamente; se não existir, o sistema utiliza o fallback **7**. Certifique‑se de que a categoria esteja cadastrada.
5. **Tabela específica:** Os parâmetros de entrada são persistidos na tabela **`cat_fugitivas_2`**, com campos: `id_gas`, `variacao_estoque`, `quantidade_transferida`, `mudanca_capacidade`, `id_documento`, `created_at`.
6. **Hierarquia flexível:** As consultas por unidade permitem incluir descendentes e filtrar por período.
7. **Resumo por empresa:** O endpoint `/empresa/{id}/resumo` oferece uma visão consolidada das emissões, incluindo distribuição por gás e evolução mensal.
8. **Auditoria:** Todos os cálculos persistentes passam por validação de auditoria, garantindo rastreabilidade e conformidade.

