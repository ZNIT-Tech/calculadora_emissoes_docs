# **Combustão Móvel 1**

Permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes da **combustão móvel** de veículos rodoviários, utilizando dados de **consumo de combustível** (litros, m³, kg) como entrada. Módulo versão 1, baseado no **tipo de veículo** e **ano de fabricação**.

---

## **Endpoints de Cálculo**

### **1. POST /api/combustao-movel-1/calcular**

Calcula emissões de GEE para um veículo específico **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "id_veiculo": 1,
  "ano_veiculo": 2020,
  "data": "2024-07-20",
  "cnpj": "00.000.000/0001-91",
  "consumo_mensal": 100.5,
  "descricao": "Veículo da frota XYZ"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_veiculo` | número | ID do tipo de veículo (consulte `/veiculos`) | 1 |
| `ano_veiculo` | número | Ano de fabricação do veículo | 2020 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-20" |
| `cnpj` | string | CNPJ da empresa para obtenção da versão GHG | "00.000.000/0001-91" |

#### **Parâmetros Condicionalmente Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `consumo_mensal` | número | Consumo mensal (litros, m³, kg) – **ou** `consumo_anual` | 100.5 |
| `consumo_anual` | número | Consumo anual – **ou** `consumo_mensal` | 1200.0 |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_empresa` | string | UUID da empresa (para metadados, não persiste) |
| `id_unidade_negocio` | string | UUID da unidade de negócio (para metadados, não persiste) |
| `descricao` | string | Descrição adicional |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-1/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_veiculo": 1,
    "ano_veiculo": 2020,
    "consumo_mensal": 100.5,
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
    "emissao_co2": 250.5,
    "emissao_ch4": 1.2,
    "emissao_n2o": 0.3,
    "emissao_total": 252.0,
    "emissao_biogenica": 10.5,
    "emissao_co2_biogenico": 5.0,
    "metadados": {
      "veiculo": {
        "id": 1,
        "nome": "Automóvel - Gasolina",
        "tipo_fossil": "Sim",
        "tipo_biocombustivel": "Não"
      },
      "consumo": {
        "mensal": 100.5,
        "anual": null
      },
      "ano_veiculo": 2020,
      "data_consumo": "2024-07-20",
      "versao_ghg": "v2025.0.1",
      "consumo_medio_km_l": 12.5
    }
  }
}
```

---

### **2. POST /api/combustao-movel-1**

Calcula e persiste emissões de GEE para um veículo **com persistência no banco** (cria documento, registro de combustão móvel 1 e emissões).

#### **Requisição:**
```json
{
  "id_veiculo": 1,
  "ano_veiculo": 2020,
  "data": "2024-07-20",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "nvl_unidade_negocio": 2,
  "consumo_mensal": 100.5,
  "descricao": "Veículo da frota XYZ",
  "cnpj_fornecedor": "12.345.678/0001-90",
  "link_referencia": "http://exemplo.com/nota-fiscal",
  "id_usuario": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_veiculo` | número | ID do tipo de veículo | 1 |
| `ano_veiculo` | número | Ano de fabricação do veículo | 2020 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-20" |
| `id_empresa` | string | UUID da empresa | "dd68b58c-9155..." |
| `id_unidade_negocio` | string | UUID da unidade de negócio | "680a4fe4-d2f1..." |
| `nvl_unidade_negocio` | número | Nível hierárquico (1–10) | 2 |

#### **Parâmetros Condicionalmente Obrigatórios:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `consumo_mensal` | número | Consumo mensal – **ou** `consumo_anual` |
| `consumo_anual` | número | Consumo anual – **ou** `consumo_mensal` |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_usuario` | string | UUID do usuário responsável |
| `descricao` | string | Descrição adicional |
| `cnpj_fornecedor` | string | CNPJ do fornecedor |
| `link_referencia` | string | Link para documento comprobatório |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-1" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_veiculo": 1,
    "ano_veiculo": 2020,
    "consumo_mensal": 100.5,
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
  "message": "Cálculo de emissões de combustão móvel 1 realizado com sucesso",
  "data": {
    "resultado_calculo": {
      "emissao_co2": 250.5,
      "emissao_ch4": 1.2,
      "emissao_n2o": 0.3,
      "emissao_total": 252.0,
      "emissao_biogenica": 10.5,
      "emissao_co2_biogenico": 5.0
    },
    "ids_gerados": {
      "documento_id": "550e8400-e29b-41d4-a716-446655440000",
      "combustao_movel_1_id": 123,
      "emissao_id": 456
    },
    "metadados": {
      "empresa": "Empresa Exemplo",
      "unidade": "Filial São Paulo",
      "veiculo": "Automóvel - Gasolina",
      "ano_veiculo": 2020,
      "data": "2024-07-20",
      "versao_ghg": "v2025.0.1"
    }
  }
}
```

#### **Notas Importantes:**
- Endpoint submete à **validação de auditoria** (`@require_audit_validation`).
- A categoria do documento é automaticamente definida como **Combustão Móvel 1** (ID recuperado dinamicamente; fallback = 1).
- A empresa e a unidade devem existir e estar associadas.
- O CNPJ da empresa é utilizado para obter a versão GHG e os fatores de emissão adequados.
- Apenas o consumo informado (`mensal` **ou** `anual`) é persistido.
- O campo `consumo_medio_km_l` é retornado nos metadados quando disponível.

---

## **Endpoints de Consulta**

### **1. GET /api/combustao-movel-1/unidade**

Consulta emissões de combustão móvel 1 agregadas por unidade de negócio, com possibilidade de incluir unidades descendentes.

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
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-1/unidade?id_unidade=680a4fe4-d2f1-4f2d-ab99-556effb557c9&nivel=2&incluir_descendentes=true" \
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
    "emissao_total": 1250.75,
    "quantidade_documentos": 8,
    "quantidade_unidades": 3,
    "detalhes_por_unidade": [
      {
        "id_unidade": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
        "nome_unidade": "Filial São Paulo",
        "documentos": [
          {
            "id": "550e8400-e29b-41d4-a716-446655440000",
            "data": "2024-07-20",
            "descricao": "Veículo da frota XYZ",
            "emissoes": {
              "co2": 250.5,
              "ch4": 1.2,
              "n2o": 0.3,
              "total": 252.0,
              "biogenica": 10.5
            }
          }
        ]
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

### **2. GET /api/combustao-movel-1/hierarquia-completa**

Retorna a hierarquia completa de emissões de combustão móvel 1 para uma empresa, agregando emissões por unidade de negócio e suas descendentes.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_empresa` | ✓ | string | UUID da empresa | — |
| `nivel_inicio` | ✗ | número | Nível hierárquico inicial | 1 |
| `data_inicio` | ✗ | string | Data inicial (YYYY-MM-DD) | — |
| `data_fim` | ✗ | string | Data final (YYYY-MM-DD) | — |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-1/hierarquia-completa?id_empresa=dd68b58c-9155-42df-97ba-8ec86953c360&nivel_inicio=1&data_inicio=2024-01-01&data_fim=2024-12-31" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
    "nome_empresa": "Empresa Exemplo",
    "emissao_total_empresa": 5280.35,
    "hierarquia": [
      {
        "id": "unidade-1-uuid",
        "nome": "Matriz",
        "nivel": 1,
        "emissao_direta": 1250.0,
        "emissao_total": 5280.35,
        "filhas": [
          {
            "id": "unidade-2-uuid",
            "nome": "Filial São Paulo",
            "nivel": 2,
            "emissao_direta": 980.5,
            "emissao_total": 4030.35,
            "filhas": [
              {
                "id": "unidade-3-uuid",
                "nome": "Depósito SP",
                "nivel": 3,
                "emissao_direta": 3049.85,
                "emissao_total": 3049.85,
                "filhas": []
              }
            ]
          }
        ]
      }
    ],
    "periodo": {
      "data_inicio": "2024-01-01",
      "data_fim": "2024-12-31"
    }
  }
}
```

---

### **3. GET /api/combustao-movel-1/detalhado**

Consulta detalhada com agregação por tipo de veículo ou por documento.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_unidade` | ✓ | string | UUID da unidade de negócio | — |
| `nivel` | ✓ | número | Nível hierárquico (1–10) | — |
| `agrupar_por` | ✗ | string | `"veiculo"` ou `"documento"` | `"veiculo"` |
| `data_inicio` | ✗ | string | Data inicial (YYYY-MM-DD) | — |
| `data_fim` | ✗ | string | Data final (YYYY-MM-DD) | — |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-1/detalhado?id_unidade=680a4fe4-d2f1-4f2d-ab99-556effb557c9&nivel=2&agrupar_por=veiculo&data_inicio=2024-01-01" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "agrupamento": "veiculo",
  "data": {
    "unidade": {
      "id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
      "nome": "Filial São Paulo"
    },
    "emissao_total": 252.0,
    "detalhes_por_veiculo": [
      {
        "id_veiculo": 1,
        "nome_veiculo": "Automóvel - Gasolina",
        "quantidade_documentos": 1,
        "emissao_total": 252.0,
        "emissao_co2": 250.5,
        "emissao_ch4": 1.2,
        "emissao_n2o": 0.3,
        "emissao_biogenica": 10.5
      }
    ]
  }
}
```

---

### **4. GET /api/combustao-movel-1/veiculos**

Lista todos os veículos disponíveis para cálculo, com suporte a filtros.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `tipo` | ✗ | string | Filtrar por tipo de veículo (ex: "Automóvel") |
| `pais` | ✗ | string | Filtrar por país (ex: "BR") |
| `versao_ghg` | ✗ | string | Filtrar por versão GHG |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-1/veiculos?versao_ghg=v2025.0.1" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "veiculo": "Automóvel - Gasolina",
      "fossil": "Sim",
      "biocombustivel": "Não",
      "pais": "BR",
      "versao_ghg": "v2025.0.1",
      "fatores_emissao": {
        "co2": 2.5,
        "ch4": 0.012,
        "n2o": 0.003,
        "co2_biogenico": 0.0
      }
    }
  ],
  "total": 1
}
```

---

### **5. GET /api/combustao-movel-1/consolidado-veiculos**

Gera um consolidado de emissões por tipo de veículo para uma empresa.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `id_empresa` | ✓ | string | UUID da empresa |
| `data_inicio` | ✗ | string | Data inicial (YYYY-MM-DD) |
| `data_fim` | ✗ | string | Data final (YYYY-MM-DD) |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-1/consolidado-veiculos?id_empresa=dd68b58c-9155-42df-97ba-8ec86953c360" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "periodo": {
      "data_inicio": null,
      "data_fim": null
    },
    "consolidado": [
      {
        "id_veiculo": 1,
        "nome_veiculo": "Automóvel - Gasolina",
        "quantidade_documentos": 10,
        "consumo_total": 1005.0,
        "emissao_total": 2520.0,
        "emissao_co2": 2505.0,
        "emissao_ch4": 12.0,
        "emissao_n2o": 3.0,
        "emissao_biogenica": 105.0
      },
      {
        "id_veiculo": 2,
        "nome_veiculo": "Automóvel - Etanol",
        "quantidade_documentos": 5,
        "consumo_total": 500.0,
        "emissao_total": 600.0,
        "emissao_co2": 0.0,
        "emissao_ch4": 2.0,
        "emissao_n2o": 0.5,
        "emissao_biogenica": 150.0
      }
    ],
    "total_emissao": 3120.0,
    "total_documentos": 15
  }
}
```

---

### **6. GET /api/combustao-movel-1/documento/{documento_id}**

Consulta detalhes de um documento específico de combustão móvel 1.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path parameter) |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-1/documento/550e8400-e29b-41d4-a716-446655440000" \
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
      "descricao": "Veículo da frota XYZ",
      "cnpj_fornecedor": "12.345.678/0001-90",
      "link_referencia": "http://exemplo.com/nota-fiscal",
      "id_categoria": 1,
      "id_usuario": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-20T14:30:00Z"
    },
    "combustao_movel_1": {
      "id": 123,
      "id_veiculo": 1,
      "ano_veiculo": 2020,
      "consumo_mensal": 100.5,
      "consumo_anual": null,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-20T14:30:00Z"
    },
    "veiculo": {
      "id": 1,
      "nome": "Automóvel - Gasolina",
      "tipo_fossil": "Sim",
      "tipo_biocombustivel": "Não"
    },
    "emissoes": {
      "id": 456,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "emissao_co2": 250.5,
      "emissao_ch4": 1.2,
      "emissao_n2o": 0.3,
      "emissao_total": 252.0,
      "emissao_biogenica": 10.5,
      "created_at": "2024-07-20T14:30:00Z"
    }
  }
}
```

---

### **7. GET /api/combustao-movel-1/resumo**

Retorna resumo estatístico das emissões de combustão móvel 1 para uma empresa.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `id_empresa` | ✓ | string | UUID da empresa |
| `ano` | ✗ | string | Ano para filtrar (formato YYYY) |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-1/resumo?id_empresa=dd68b58c-9155-42df-97ba-8ec86953c360&ano=2024" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "ano": 2024,
    "total_documentos": 10,
    "total_unidades": 5,
    "total_emissao": 1500.75,
    "media_emissao": 150.08,
    "min_emissao": 50.25,
    "max_emissao": 300.50,
    "total_co2": 1200.60,
    "total_ch4": 1.5,
    "total_n2o": 0.8
  }
}
```

---

## **Autenticação e Permissões**

Todos os endpoints exigem **API Key** no header `X-API-Key` e a permissão adequada.

| Endpoint | Método | Permissão Requerida |
|----------|--------|---------------------|
| `/combustao-movel-1/calcular` | POST | `calcular:escopo1` |
| `/combustao-movel-1` (persistência) | POST | `calcular:escopo1` |
| `/unidade` | GET | `consultar:resultados` |
| `/hierarquia-completa` | GET | `consultar:resultados` |
| `/detalhado` | GET | `consultar:resultados` |
| `/veiculos` | GET | `consultar:catalogo` |
| `/consolidado-veiculos` | GET | `consultar:resultados` |
| `/documento/{documento_id}` | GET | `consultar:resultados` |
| `/resumo` | GET | `consultar:resultados` |

**Header de Autenticação:**
```http
X-API-Key: sua-chave-api-aqui
```

---

## **Regras de Cálculo Atualizadas**

### **Versão GHG**
- A versão GHG é obtida automaticamente a partir do **CNPJ** informado (endpoint `/calcular`) ou do CNPJ da empresa cadastrada (endpoint com persistência).
- Os fatores de emissão variam conforme a **versão GHG**, o **tipo de veículo** e, opcionalmente, o **ano do veículo**.

### **Fluxo de Cálculo:**
1. **Obter versão GHG** através do CNPJ.
2. **Identificar veículo** e buscar fatores de emissão na tabela `lista_veiculos_combustiveis` (específicos da versão GHG).
3. **Validar ano do veículo** (não há faixa rígida, mas deve ser numérico).
4. **Calcular emissões** com base no consumo informado (litros, m³, kg):
   - Emissão CO₂ = Consumo × Fator CO₂ (fóssil)
   - Emissão CH₄ = Consumo × Fator CH₄
   - Emissão N₂O = Consumo × Fator N₂O
   - Emissão Total = Σ(Emissões × Potenciais de Aquecimento Global – GWP)
   - Emissão biogênica (CO₂ de origem renovável) é calculada apenas para veículos que utilizam biocombustíveis.
5. **Consumo médio** (km/l): se disponível na base, é retornado nos metadados.
6. **Consumo**: deve ser informado **consumo_mensal** **ou** **consumo_anual**. Apenas o campo preenchido é utilizado e persistido.

### **Validações:**
- **CNPJ obrigatório** em `/calcular`; no fluxo persistente, o CNPJ é obtido da empresa.
- **Empresa e unidade** devem existir e estar ativas.
- **Nível da unidade** deve estar entre 1 e 10.
- **Categoria de Combustão Móvel 1** deve estar configurada no sistema (fallback ID = 1).
- **Consumo deve ser maior que zero**.
- **Auditoria automática** é aplicada no fluxo persistente.

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Campos obrigatórios ausentes ou inválidos | Verifique o corpo da requisição |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Verifique suas permissões de usuário |
| `404` | Recurso não encontrado | Verifique IDs de empresa, unidade, veículo |
| `500` | Erro interno do servidor | Consulte os logs do sistema |

---

## **Exemplos de Erro Comuns**

### **Consumo não informado:**
```json
{
  "error": "Informe consumo_mensal ou consumo_anual"
}
```

### **Consumo não positivo:**
```json
{
  "error": "consumo_mensal deve ser maior que zero"
}
```

### **Veículo não encontrado:**
```json
{
  "error": "Veículo ID 99 não encontrado para versão GHG v2025.0.1"
}
```

### **Empresa não encontrada:**
```json
{
  "error": "Empresa não encontrada"
}
```

### **CNPJ não localizado (cálculo sem persistência):**
```json
{
  "error": "Não foi possível obter a versão GHG para o CNPJ: 00.000.000/0001-91"
}
```

### **Versão GHG não encontrada para o CNPJ:**
```json
{
  "error": "Versão GHG não encontrada para o CNPJ: 00.000.000/0001-91"
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
2. **Versão GHG dinâmica:** Os fatores de emissão são determinados pela versão GHG vigente para o CNPJ da empresa.
3. **Consumo mensal vs. anual:** Forneça **apenas um** dos campos; o sistema ignorará o outro e persistirá somente o preenchido.
4. **Ano do veículo:** É obrigatório e deve ser um número inteiro. Pode influenciar a seleção de fatores de emissão (ex.: veículos mais novos podem ter padrões diferentes).
5. **Categoria:** A categoria **Combustão Móvel 1** é obtida dinamicamente; se não existir, o sistema utiliza o fallback `1`. Certifique-se de que a categoria está cadastrada.
6. **Consumo médio (km/l):** Quando disponível, é retornado nos metadados do cálculo, auxiliando na validação dos dados.
7. **Hierarquia flexível:** As consultas de hierarquia (`unidade`, `hierarquia-completa`) permitem filtrar por período e incluir ou não unidades descendentes.
8. **Consultas específicas:** O módulo oferece endpoints dedicados para análise por veículo (`consolidado-veiculos`) e agregação detalhada (`detalhado`).
9. **Auditoria:** Todos os cálculos persistentes passam por validação de auditoria, garantindo rastreabilidade e conformidade.

---
