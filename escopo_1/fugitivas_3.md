# **Fugitivas 3**

Permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes de **emissões fugitivas** associadas a **equipamentos de refrigeração**, utilizando metodologia que considera a **carga de unidades novas**, a **capacidade instalada em operação** e a **capacidade de unidades dispensadas**. Os fatores de emissão são específicos para cada **tipo de equipamento** e **gás refrigerante**, e variam conforme a **versão GHG** da empresa.

---

## **Endpoints de Cálculo**

### **1. POST /api/fugitivas-3/calcular**

Calcula emissões de GEE para uma operação com equipamentos de refrigeração **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "id_gas": 15,
  "id_refrigerador": 2,
  "carga_unidades_novas": 50.0,
  "capacidade_unidades_operacao": 1000.0,
  "capacidade_unidades_dispensadas": 200.0,
  "data": "2024-07-20",
  "cnpj": "00.000.000/0001-91"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_gas` | número | ID do gás refrigerante (consulte `/gases`) | 15 |
| `id_refrigerador` | número | ID do tipo de equipamento (consulte `/refrigeradores`) | 2 |
| `carga_unidades_novas` | número | Carga total de gás em unidades novas (kg) | 50.0 |
| `capacidade_unidades_operacao` | número | Capacidade total de gás das unidades em operação (kg) | 1000.0 |
| `capacidade_unidades_dispensadas` | número | Capacidade total de gás das unidades dispensadas (kg) | 200.0 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-20" |
| `cnpj` | string | CNPJ da empresa para obtenção da versão GHG | "00.000.000/0001-91" |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_empresa` | string | UUID da empresa (para metadados, não persiste) |
| `id_unidade_negocio` | string | UUID da unidade de negócio (para metadados, não persiste) |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/fugitivas-3/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_gas": 15,
    "id_refrigerador": 2,
    "carga_unidades_novas": 50.0,
    "capacidade_unidades_operacao": 1000.0,
    "capacidade_unidades_dispensadas": 200.0,
    "data": "2024-07-20",
    "cnpj": "00.000.000/0001-91"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões realizado com sucesso",
  "data": {
    "emissao_total": 125.5,
    "emissao_co2": 0,
    "emissao_ch4": 0,
    "emissao_n2o": 0,
    "emissao_biogenica": 0,
    "fator_gwp": 1430,
    "metadados": {
      "gas": {
        "id": 15,
        "nome": "R-404A",
        "familia_top": "HFC",
        "gwp": 1430
      },
      "equipamento": {
        "id": 2,
        "nome": "Câmara frigorífica",
        "value_a": 0.1,
        "value_b": 0.05,
        "value_c": 0.02,
        "value_d": 0.01
      },
      "parametros": {
        "carga_unidades_novas": 50.0,
        "capacidade_unidades_operacao": 1000.0,
        "capacidade_unidades_dispensadas": 200.0
      },
      "data_calculo": "2024-07-20"
    }
  }
}
```

#### **Notas sobre o Cálculo:**
A emissão total (em tCO₂e) é calculada internamente pela função `calcular_emissoes_fugitivas_3`, que utiliza:

- Fatores de emissão do equipamento (`value_a`, `value_b`, `value_c`, `value_d`) obtidos da tabela `lista_refrigeradores`.
- GWP do gás obtido da tabela `lista_gwp` (dependente da versão GHG).
- A fórmula segue metodologia específica para fugitivas em refrigeração (não detalhada no blueprint, mas presume-se combinação linear dos parâmetros com os fatores do equipamento).

Os campos `emissao_co2`, `emissao_ch4`, `emissao_n2o`, `emissao_biogenica` são preenchidos com **zero** (este módulo trata apenas gases de alto GWP, não CO₂/CH₄/N₂O fósseis).

---

### **2. POST /api/fugitivas-3**

Calcula e persiste emissões de GEE para uma operação com equipamentos de refrigeração **com persistência no banco** (cria documento, registro de fugitivas 3 e emissões).

#### **Requisição:**
```json
{
  "id_gas": 15,
  "id_refrigerador": 2,
  "carga_unidades_novas": 50.0,
  "capacidade_unidades_operacao": 1000.0,
  "capacidade_unidades_dispensadas": 200.0,
  "data": "2024-07-20",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "nvl_unidade_negocio": 2,
  "descricao": "Emissões de sistema de refrigeração comercial",
  "link_referencia": "http://exemplo.com/relatorio",
  "id_usuario": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_gas` | número | ID do gás refrigerante | 15 |
| `id_refrigerador` | número | ID do tipo de equipamento | 2 |
| `carga_unidades_novas` | número | Carga em unidades novas (kg) | 50.0 |
| `capacidade_unidades_operacao` | número | Capacidade de unidades em operação (kg) | 1000.0 |
| `capacidade_unidades_dispensadas` | número | Capacidade de unidades dispensadas (kg) | 200.0 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-20" |
| `id_empresa` | string | UUID da empresa | "dd68b58c-9155..." |
| `id_unidade_negocio` | string | UUID da unidade de negócio | "680a4fe4-d2f1..." |
| `nvl_unidade_negocio` | número | Nível hierárquico (1–10) | 2 |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_usuario` | string | UUID do usuário responsável |
| `descricao` | string | Descrição adicional |
| `link_referencia` | string | Link para documento comprobatório |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/fugitivas-3" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_gas": 15,
    "id_refrigerador": 2,
    "carga_unidades_novas": 50.0,
    "capacidade_unidades_operacao": 1000.0,
    "capacidade_unidades_dispensadas": 200.0,
    "data": "2024-07-20",
    "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
    "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
    "nvl_unidade_negocio": 2
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões de fugitivas 3 realizado com sucesso",
  "data": {
    "resultado_calculo": {
      "emissao_total": 125.5,
      "emissao_co2": 0,
      "emissao_ch4": 0,
      "emissao_n2o": 0,
      "emissao_biogenica": 0,
      "fator_gwp": 1430
    },
    "ids_gerados": {
      "documento_id": "550e8400-e29b-41d4-a716-446655440000",
      "fugitivas_3_id": 123,
      "emissao_id": 456
    },
    "metadados": {
      "empresa": "Empresa Exemplo",
      "unidade": "Filial São Paulo",
      "gas": "R-404A",
      "equipamento": "Câmara frigorífica",
      "data": "2024-07-20"
    }
  }
}
```

#### **Notas Importantes:**
- Endpoint submete à **validação de auditoria** (`@require_audit_validation`).
- A categoria do documento é obtida dinamicamente via `db.get_categoria_fugitivas_3_id()` (fallback = **8**).
- A empresa e a unidade devem existir e estar ativas.
- O CNPJ da empresa é utilizado para obter a versão GHG, que determina os fatores de emissão do equipamento e o GWP do gás.
- Os parâmetros de entrada são persistidos na tabela **`cat_fugitivas_3`**.
- As emissões são persistidas na tabela `emissoes`; os campos específicos de CO₂, CH₄ e N₂O permanecem zerados, pois este módulo trata exclusivamente de HFCs, PFCs, SF₆ e outros gases de alto GWP.

---

## **Endpoints de Consulta**

### **1. GET /api/fugitivas-3/unidade**

Consulta emissões de fugitivas 3 agregadas por unidade de negócio, com possibilidade de incluir unidades descendentes.

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
curl "http://localhost:8080/api/fugitivas-3/unidade?id_unidade=680a4fe4-d2f1-4f2d-ab99-556effb557c9&nivel=2&incluir_descendentes=true" \
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
    "emissao_total": 125.5,
    "quantidade_documentos": 1,
    "quantidade_unidades": 1,
    "detalhes": [
      {
        "documento_id": "550e8400-e29b-41d4-a716-446655440000",
        "data": "2024-07-20",
        "descricao": "Fugitivas 3 - Gás: R-404A, Equipamento: Câmara frigorífica",
        "unidade": {
          "id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
          "nome": "Filial São Paulo",
          "nivel": 2
        },
        "emissoes": {
          "total": 125.5,
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

### **2. GET /api/fugitivas-3/{documento_id}/detalhes**

Consulta detalhes completos de um documento específico de fugitivas 3.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path parameter) |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/fugitivas-3/550e8400-e29b-41d4-a716-446655440000/detalhes" \
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
      "descricao": "Fugitivas 3 - Gás: R-404A, Equipamento: Câmara frigorífica",
      "link_referencia": null,
      "id_categoria": 8,
      "id_usuario": null,
      "created_at": "2024-07-20T14:30:00Z"
    },
    "fugitivas_3": {
      "id": 123,
      "id_gas": 15,
      "id_refrigerador": 2,
      "carga_unidades_novas": 50.0,
      "capacidade_unidades_operacao": 1000.0,
      "capacidade_unidades_dispensadas": 200.0,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-20T14:30:00Z"
    },
    "gas": {
      "id": 15,
      "gas": "R-404A",
      "familia_top": "HFC",
      "gwp": 1430,
      "referencia": "IPCC AR5"
    },
    "equipamento": {
      "id": 2,
      "equipamento": "Câmara frigorífica",
      "value_a": 0.1,
      "value_b": 0.05,
      "value_c": 0.02,
      "value_d": 0.01,
      "versao_ghg": "v2025.0.1"
    },
    "emissoes": {
      "id": 456,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "emissao_co2": 0,
      "emissao_ch4": 0,
      "emissao_n2o": 0,
      "emissao_total": 125.5,
      "emissao_biogenica": 0,
      "created_at": "2024-07-20T14:30:00Z"
    }
  }
}
```

---

### **3. GET /api/fugitivas-3/gases**

Lista todos os gases disponíveis para cálculo de fugitivas 3 (compartilha a mesma tabela `lista_gwp` dos outros módulos fugitivas).

#### **Parâmetros:**
Nenhum.

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/fugitivas-3/gases" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": [
    {
      "id": 15,
      "gas": "R-404A",
      "familia_top": "HFC",
      "gwp": 1430,
      "referencia": "IPCC AR5"
    },
    {
      "id": 16,
      "gas": "R-410A",
      "familia_top": "HFC",
      "gwp": 2088,
      "referencia": "IPCC AR5"
    }
  ],
  "total": 2
}
```

---

### **4. GET /api/fugitivas-3/refrigeradores**

Lista todos os equipamentos refrigeradores disponíveis para fugitivas 3, opcionalmente filtrados por versão GHG.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `versao_ghg` | ✗ | string | Versão GHG para filtrar os fatores de emissão |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/fugitivas-3/refrigeradores?versao_ghg=v2025.0.1" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "equipamento": "Compressor hermético",
      "value_a": 0.15,
      "value_b": 0.08,
      "value_c": 0.03,
      "value_d": 0.015,
      "versao_ghg": "v2025.0.1"
    },
    {
      "id": 2,
      "equipamento": "Câmara frigorífica",
      "value_a": 0.1,
      "value_b": 0.05,
      "value_c": 0.02,
      "value_d": 0.01,
      "versao_ghg": "v2025.0.1"
    }
  ],
  "total": 2
}
```

---

### **5. GET /api/fugitivas-3/hierarquia-completa**

Retorna a hierarquia completa de emissões de fugitivas 3 para uma empresa, agregando emissões por unidade de negócio e suas descendentes.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_empresa` | ✓ | string | UUID da empresa | — |
| `nivel_inicio` | ✗ | número | Nível hierárquico inicial (1–10) | 1 |
| `ano` | ✗ | string | Ano para filtrar (formato YYYY) | — |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/fugitivas-3/hierarquia-completa?id_empresa=dd68b58c-9155-42df-97ba-8ec86953c360&nivel_inicio=1&ano=2024" \
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
| `/fugitivas-3/calcular` | POST | `calcular:escopo1` |
| `/fugitivas-3` (persistência) | POST | `calcular:escopo1` |
| `/fugitivas-3/unidade` | GET | `consultar:resultados` |
| `/fugitivas-3/{documento_id}/detalhes` | GET | `consultar:resultados` |
| `/fugitivas-3/gases` | GET | `consultar:resultados` |
| `/fugitivas-3/refrigeradores` | GET | `consultar:resultados` |
| `/fugitivas-3/hierarquia-completa` | GET | `consultar:resultados` |

**Header de Autenticação:**
```http
X-API-Key: sua-chave-api-aqui
```

---

## **Regras de Cálculo Atualizadas**

### **Metodologia**
O cálculo das emissões fugitivas de equipamentos de refrigeração segue metodologia baseada em:

1. **Obtenção da versão GHG** a partir do CNPJ informado (ou CNPJ da empresa, no fluxo persistente).
2. **Identificação do gás** e obtenção do **GWP** correspondente à versão GHG.
3. **Identificação do equipamento** e obtenção dos **fatores de emissão** (`value_a`, `value_b`, `value_c`, `value_d`) específicos da versão GHG.
4. **Cálculo da emissão** (em kg do gás) utilizando os parâmetros de entrada e os fatores do equipamento.  
   *(A fórmula exata não está exposta no blueprint, mas presume-se uma combinação linear do tipo:*  
   `Emissão (kg) = (carga_unidades_novas × value_a) + (capacidade_unidades_operacao × value_b) + (capacidade_unidades_dispensadas × value_c)` *– ou similar.)*
5. **Conversão para tCO₂e** multiplicando a emissão em kg do gás pelo GWP e dividindo por 1000.
6. **Distribuição das emissões**: Como os gases tratados são predominantemente HFCs, PFCs e SF₆, os campos `emissao_co2`, `emissao_ch4`, `emissao_n2o` e `emissao_biogenica` são preenchidos com **zero**. Toda a emissão é registrada em `emissao_total`.

### **Dependência da Versão GHG**
- **Fatores de emissão dos equipamentos** (`value_a`, `value_b`, ...) são armazenados na tabela `lista_refrigeradores` e podem variar conforme a versão GHG.
- **GWP dos gases** é armazenado na tabela `lista_gwp` e também varia conforme a versão GHG.
- A versão GHG é obtida dinamicamente a partir do **CNPJ** da empresa (ou do `cnpj` informado no cálculo sem persistência).

### **Validações:**
- Todos os valores de carga e capacidade devem ser **não negativos**.
- O nível da unidade (`nvl_unidade_negocio`) deve estar entre 1 e 10.
- A empresa e a unidade devem existir no banco (fluxo persistente).
- O gás e o equipamento devem existir e estar associados à versão GHG da empresa.

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Campos obrigatórios ausentes ou inválidos | Verifique o corpo da requisição |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Verifique suas permissões de usuário |
| `404` | Recurso não encontrado | Verifique IDs de empresa, unidade, gás, equipamento |
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

### **Equipamento refrigerador não encontrado:**
```json
{
  "error": "Equipamento refrigerador ID 99 não encontrado"
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

### **Versão GHG não encontrada para o CNPJ:**
```json
{
  "error": "Não foi possível obter a versão GHG para o CNPJ: 00.000.000/0001-91"
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
2. **Dependência de versão GHG:** Os fatores de emissão dos equipamentos e os GWP dos gases são estritamente dependentes da versão GHG obtida a partir do CNPJ. Certifique-se de que o CNPJ informado esteja cadastrado e possua uma versão GHG ativa.
3. **Equipamentos refrigeradores:** A lista de equipamentos é dinâmica e versionada. Consulte o endpoint `/refrigeradores` para obter os IDs e fatores disponíveis.
4. **Categoria:** A categoria de Fugitivas 3 é obtida dinamicamente; se não existir, o sistema utiliza o fallback **8**. Certifique-se de que a categoria esteja cadastrada.
5. **Tabela específica:** Os parâmetros de entrada são persistidos na tabela **`cat_fugitivas_3`**, com campos: `id_gas`, `id_refrigerador`, `carga_unidades_novas`, `capacidade_unidades_operacao`, `capacidade_unidades_dispensadas`, `id_documento`, `created_at`.
6. **Emissões sem discriminação por gás:** Diferentemente de outros módulos, as emissões de fugitivas 3 não são alocadas nos campos `emissao_co2`, `emissao_ch4`, `emissao_n2o`. Toda a emissão (em tCO₂e) é registrada em `emissao_total`. Os demais campos permanecem zerados.
7. **Hierarquia flexível:** A consulta de hierarquia permite definir o nível inicial e inclui recursivamente todas as unidades descendentes.
8. **Auditoria:** Todos os cálculos persistentes passam por validação de auditoria, garantindo rastreabilidade e conformidade.

---

