# **Fugitivas 1**

Permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes de **emissões fugitivas** – especificamente aquelas associadas a **equipamentos de refrigeração, ar condicionado e bombas de calor**, com base no balanço de carga de gás (carga nova, capacidade, recarga, dispensas e recuperação). Módulo versão 1, aplicável a HFCs, PFCs, SF6 e outros gases de alto GWP, bem como CO₂, CH₄ e N₂O quando aplicável.

---

## **Endpoints de Cálculo**

### **1. POST /api/fugitivas-1/calcular**

Calcula emissões de GEE para uma operação com gases refrigerantes **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "gwp_id": 5,
  "novas_carga": 100.0,
  "novas_capacidade": 20.0,
  "existentes_recarga": 30.0,
  "dispensadas_capacidade": 10.0,
  "dispensadas_recuperado": 5.0,
  "data": "2024-07-20"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `gwp_id` | número | ID do gás/refrigerante (consulte `/gases`) | 5 |
| `novas_carga` | número | Carga de gás em novos equipamentos (kg) | 100.0 |
| `novas_capacidade` | número | Capacidade de gás retirada de novos equipamentos (kg) | 20.0 |
| `existentes_recarga` | número | Recarga em equipamentos existentes (kg) | 30.0 |
| `dispensadas_capacidade` | número | Capacidade nominal de equipamentos dispensados (kg) | 10.0 |
| `dispensadas_recuperado` | número | Gás recuperado de equipamentos dispensados (kg) | 5.0 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-20" |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_empresa` | string | UUID da empresa (para metadados, não persiste) |
| `id_unidade_negocio` | string | UUID da unidade de negócio (para metadados, não persiste) |
| `ano` | número | Ano de referência (caso diferente do ano da data) |
| `escopo` | string | Escopo da emissão (padrão: `"escopo1"`) |
| `cnpj_cliente` | string | CNPJ do cliente (para rastreabilidade) |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/fugitivas-1/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "gwp_id": 5,
    "novas_carga": 100.0,
    "novas_capacidade": 20.0,
    "existentes_recarga": 30.0,
    "dispensadas_capacidade": 10.0,
    "dispensadas_recuperado": 5.0,
    "data": "2024-07-20"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões realizado com sucesso",
  "data": {
    "emissao_total": 1150.0,
    "fator_gwp": 2300,
    "emissoes_hfc": 1150.0,
    "emissoes_pfc": 0,
    "emissao_co2": 0,
    "emissao_ch4": 0,
    "emissao_n2o": 0,
    "emissao_biogenica": 0,
    "metadados": {
      "gas": {
        "id": 5,
        "nome": "R-134a",
        "familia_top": "HFC",
        "gwp": 2300
      },
      "parametros": {
        "novas_carga": 100.0,
        "novas_capacidade": 20.0,
        "existentes_recarga": 30.0,
        "dispensadas_capacidade": 10.0,
        "dispensadas_recuperado": 5.0
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
emissao_total = (novas_carga - novas_capacidade + existentes_recarga + dispensadas_capacidade - dispensadas_recuperado) × GWP ÷ 1000
```

Os campos `emissoes_hfc`, `emissoes_pfc`, `emissao_co2`, `emissao_ch4`, `emissao_n2o` são preenchidos conforme o `gwp_id`:
- `gwp_id = 1` → emissão como CO₂ (fóssil)
- `gwp_id = 2` → emissão como CH₄
- `gwp_id = 3` → emissão como N₂O
- demais IDs → emissão como HFC/PFC (campo `emissoes_hfc` ou `emissoes_pfc`)

---

### **2. POST /api/fugitivas-1**

Calcula e persiste emissões de GEE para uma operação com gases refrigerantes **com persistência no banco** (cria documento, registro de fugitivas e emissões).

#### **Requisição:**
```json
{
  "gwp_id": 5,
  "novas_carga": 100.0,
  "novas_capacidade": 20.0,
  "existentes_recarga": 30.0,
  "dispensadas_capacidade": 10.0,
  "dispensadas_recuperado": 5.0,
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "nvl_unidade_negocio": 2,
  "data": "2024-07-20",
  "descricao": "Recarga de sistema de climatização",
  "link_referencia": "http://exemplo.com/ordem-servico",
  "id_usuario": "550e8400-e29b-41d4-a716-446655440000",
  "ano": 2024,
  "escopo": "escopo1",
  "cnpj_cliente": "12.345.678/0001-90"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `gwp_id` | número | ID do gás/refrigerante | 5 |
| `novas_carga` | número | Carga em novos equipamentos (kg) | 100.0 |
| `novas_capacidade` | número | Capacidade retirada de novos equipamentos (kg) | 20.0 |
| `existentes_recarga` | número | Recarga em equipamentos existentes (kg) | 30.0 |
| `dispensadas_capacidade` | número | Capacidade de equipamentos dispensados (kg) | 10.0 |
| `dispensadas_recuperado` | número | Gás recuperado de equipamentos dispensados (kg) | 5.0 |
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
| `ano` | número | Ano de referência (padrão = ano da `data`) |
| `escopo` | string | Escopo da emissão (padrão: `"escopo1"`) |
| `cnpj_cliente` | string | CNPJ do cliente (para rastreabilidade) |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/fugitivas-1" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "gwp_id": 5,
    "novas_carga": 100.0,
    "novas_capacidade": 20.0,
    "existentes_recarga": 30.0,
    "dispensadas_capacidade": 10.0,
    "dispensadas_recuperado": 5.0,
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
  "message": "Cálculo de emissões de fugitivas 1 realizado com sucesso",
  "data": {
    "resultado_calculo": {
      "emissao_total": 1150.0,
      "fator_gwp": 2300,
      "emissoes_hfc": 1150.0,
      "emissoes_pfc": 0,
      "emissao_co2": 0,
      "emissao_ch4": 0,
      "emissao_n2o": 0,
      "emissao_biogenica": 0
    },
    "ids_gerados": {
      "documento_id": "550e8400-e29b-41d4-a716-446655440000",
      "fugitivas_1_id": 123,
      "emissao_id": 456
    },
    "metadados": {
      "empresa": "Empresa Exemplo",
      "unidade": "Filial São Paulo",
      "gas": "R-134a",
      "familia_gas": "HFC",
      "data": "2024-07-20",
      "ano": 2024,
      "escopo": "escopo1"
    }
  }
}
```

#### **Notas Importantes:**
- Endpoint submete à **validação de auditoria** (`@require_audit_validation`).
- A categoria do documento é obtida dinamicamente via `db.get_categoria_fugitivas_1_id()` (fallback = 6).
- A empresa e a unidade devem existir e estar ativas.
- A versão GHG **não é obrigatória** para este módulo; caso não seja possível obtê-la, utiliza-se um valor padrão.
- Os parâmetros de entrada são persistidos na tabela `cat_fugitivas_1`.
- As emissões são persistidas na tabela `emissoes`.

---

## **Endpoints de Consulta**

### **1. GET /api/fugitivas-1/unidade**

Consulta emissões de fugitivas 1 agregadas por unidade de negócio, com possibilidade de incluir unidades descendentes.

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
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/fugitivas-1/unidade?id_unidade=680a4fe4-d2f1-4f2d-ab99-556effb557c9&nivel=2&incluir_descendentes=true" \
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
        "descricao": "Fugitivas 1 - Gás: R-134a",
        "unidade": {
          "id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
          "nome": "Filial São Paulo",
          "nivel": 2
        },
        "emissoes": {
          "total": 1150.0,
          "hfc": 1150.0,
          "pfc": 0,
          "co2": 0,
          "ch4": 0,
          "n2o": 0,
          "biogenica": 0
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

### **2. GET /api/fugitivas-1/{documento_id}/detalhes**

Consulta detalhes completos de um documento específico de fugitivas 1.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path parameter) |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/fugitivas-1/550e8400-e29b-41d4-a716-446655440000/detalhes" \
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
      "descricao": "Fugitivas 1 - Gás: R-134a",
      "link_referencia": null,
      "id_categoria": 6,
      "id_usuario": null,
      "created_at": "2024-07-20T14:30:00Z"
    },
    "fugitivas_1": {
      "id": 123,
      "id_gas": 5,
      "novas_carga": 100.0,
      "novas_capacidade": 20.0,
      "existentes_recarga": 30.0,
      "dispensadas_capacidade": 10.0,
      "dispensadas_recuperada": 5.0,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-20T14:30:00Z"
    },
    "gas": {
      "id": 5,
      "gas": "R-134a",
      "familia_top": "HFC",
      "gwp": 2300,
      "referencia": "IPCC AR5"
    },
    "emissoes": {
      "id": 456,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "emissao_co2": 0,
      "emissao_ch4": 0,
      "emissao_n2o": 0,
      "emissao_total": 1150.0,
      "emissao_biogenica": 0,
      "created_at": "2024-07-20T14:30:00Z"
    }
  }
}
```

---

### **3. GET /api/fugitivas-1/gases**

Lista todos os gases disponíveis para cálculo de fugitivas 1, com seus respectivos GWP e família.

#### **Parâmetros:**
Nenhum.

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/fugitivas-1/gases" \
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
      "gas": "R-134a",
      "familia_top": "HFC",
      "gwp": 2300,
      "referencia": "IPCC AR5"
    }
  ],
  "total": 4
}
```

---

### **4. GET /api/fugitivas-1/hierarquia-completa**

Retorna a hierarquia completa de emissões de fugitivas 1 para uma empresa, agregando emissões por unidade de negócio e suas descendentes.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_empresa` | ✓ | string | UUID da empresa | — |
| `nivel_inicio` | ✗ | número | Nível hierárquico inicial (1–10) | 1 |
| `ano` | ✗ | string | Ano para filtrar (formato YYYY) | — |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/fugitivas-1/hierarquia-completa?id_empresa=dd68b58c-9155-42df-97ba-8ec86953c360&nivel_inicio=1&ano=2024" \
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
    "emissao_total_empresa": 5280.35,
    "nivel_inicio": 1,
    "quantidade_unidades_raiz": 3,
    "periodo": {
      "ano": "2024",
      "filtro_aplicado": true
    },
    "hierarquia": [
      {
        "id": "unidade-1-uuid",
        "nivel": 1,
        "nome": "Matriz",
        "emissao_direta": 1250.0,
        "emissao_total": 5280.35,
        "quantidade_documentos": 5,
        "filhas": [
          {
            "id": "unidade-2-uuid",
            "nivel": 2,
            "nome": "Filial São Paulo",
            "emissao_direta": 980.5,
            "emissao_total": 4030.35,
            "quantidade_documentos": 8,
            "filhas": [
              {
                "id": "unidade-3-uuid",
                "nivel": 3,
                "nome": "Depósito SP",
                "emissao_direta": 3049.85,
                "emissao_total": 3049.85,
                "quantidade_documentos": 3,
                "filhas": []
              }
            ]
          }
        ]
      }
    ]
  }
}
```

---

## **Autenticação e Permissões**

Todos os endpoints exigem **API Key** no header `X-API-Key` e a permissão adequada.

| Endpoint | Método | Permissão Requerida |
|----------|--------|---------------------|
| `/fugitivas-1/calcular` | POST | `calcular:escopo1` |
| `/fugitivas-1` (persistência) | POST | `calcular:escopo1` |
| `/fugitivas-1/unidade` | GET | `consultar:resultados` |
| `/fugitivas-1/{documento_id}/detalhes` | GET | `consultar:resultados` |
| `/fugitivas-1/gases` | GET | `consultar:resultados` |
| `/fugitivas-1/hierarquia-completa` | GET | `consultar:resultados` |

**Header de Autenticação:**
```http
X-API-Key: sua-chave-api-aqui
```

---

## **Regras de Cálculo Atualizadas**

### **Metodologia**
O cálculo segue a abordagem de **balanço de massa** para equipamentos que contêm gases de efeito estufa:

```
Emissão (kg) = (Carga em novos equipamentos)
              - (Capacidade retirada de novos equipamentos)
              + (Recarga em equipamentos existentes)
              + (Capacidade nominal de equipamentos dispensados)
              - (Gás recuperado de equipamentos dispensados)
```

O resultado em kg do gás é então convertido para **kgCO₂e** multiplicando pelo **Potencial de Aquecimento Global (GWP)** do gás específico e dividindo por 1000 (se o GWP estiver em base kgCO₂e/kg).

### **Gases e GWP**
- A tabela de gases (`gwp_id`) é obtida do banco de dados e contém o valor de GWP para cada substância.
- Os gases são classificados por família (`familia_top`): CO₂, CH₄, N₂O, HFCs, PFCs, SF₆, etc.
- Para gases distintos de CO₂/CH₄/N₂O, a emissão é alocada nos campos `emissoes_hfc` ou `emissoes_pfc` (conforme a família).

### **Validações:**
- Todos os valores de massa (carga, capacidade, recarga, recuperado) devem ser **não negativos**.
- A data deve estar no formato ISO (`YYYY-MM-DD`).
- O nível da unidade (`nvl_unidade_negocio`) deve estar entre 1 e 10.
- A empresa e a unidade devem existir no banco (fluxo com persistência).

### **Versão GHG**
- **Este módulo não depende da versão GHG** para definição de fatores de emissão. O GWP é obtido diretamente da tabela de gases.
- Caso o sistema tente obter a versão GHG a partir do CNPJ e falhe, é utilizado um valor padrão (`"v2025.0.1"`) sem impacto no cálculo.

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Campos obrigatórios ausentes ou inválidos | Verifique o corpo da requisição |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Verifique suas permissões de usuário |
| `404` | Recurso não encontrado | Verifique IDs de empresa, unidade, gás |
| `500` | Erro interno do servidor | Consulte os logs do sistema |

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

---

## **Observações Importantes**

1. **Dois modos de cálculo:** Utilize `/calcular` para testes e simulações; utilize o POST sem `/calcular` para lançamento oficial com persistência e auditoria.
2. **Sem dependência de versão GHG:** Diferentemente de outros módulos do Escopo 1, o cálculo de fugitivas 1 não utiliza fatores de emissão parametrizados por versão GHG – o GWP é fixo para cada gás na tabela.
3. **Balanço de massa:** A fórmula implementada reflete a metodologia do GHG Protocol para emissões fugitivas em refrigeração e ar condicionado.
4. **Categoria:** A categoria de Fugitivas 1 é obtida dinamicamente; se não existir, o sistema utiliza o fallback `6`. Certifique-se de que a categoria esteja cadastrada.
5. **Tabela específica:** Os parâmetros de entrada são persistidos na tabela `cat_fugitivas_1`, que possui campos como `id_gas`, `novas_carga`, `novas_capacidade`, `existentes_recarga`, `dispensadas_capacidade`, `dispensadas_recuperada`.
6. **Hierarquia flexível:** A consulta de hierarquia permite definir o nível inicial e inclui recursivamente todas as unidades descendentes.
7. **Auditoria:** Todos os cálculos persistentes passam por validação de auditoria, garantindo rastreabilidade e conformidade.
8. **Gases disponíveis:** Consulte o endpoint `/gases` para obter a lista completa de substâncias e seus respectivos GWPs.

