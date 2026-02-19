# **Downstream 2 (Transporte e Distribuição de Combustíveis) – Escopo 3**

Permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes do transporte e distribuição a jusante (*downstream*) de combustíveis – etapa da cadeia de valor que compreende a distribuição dos produtos vendidos pela empresa, incluindo combustíveis líquidos e gasosos.

---

## **Endpoints de Cálculo**

### **1. POST /api/downstream-2/calcular**

Calcula emissões de GEE para uma operação de downstream 2 **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "id_combustivel": 25,
  "consumo_mensal": 8000.0,
  "consumo_anual": 0,
  "data": "2024-08-15",
  "unidade": "l",
  "cnpj": "00.000.000/0001-91",
  "cnpj_usuario": "",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_combustivel` | número | ID do combustível (tabela `lista_combustivel_composto`) | 25 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-08-15" |
| `unidade` | string | Unidade de medida do consumo: `"l"` (litros) ou `"m3"` (metros cúbicos) | "l" |
| `cnpj` | string | CNPJ da empresa para obtenção da versão GHG | "00.000.000/0001-91" |

#### **Parâmetros de Consumo (pelo menos um obrigatório):**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `consumo_mensal` | número | Consumo no mês (na unidade especificada) |
| `consumo_anual` | número | Consumo anual (se fornecido, substitui o mensal para cálculo anual) |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `cnpj_usuario` | string | CNPJ do usuário (para casos específicos, como na Colômbia) |
| `id_empresa` | string | ID da empresa (para metadados) |
| `id_unidade_negocio` | string | ID da unidade de negócio (para metadados) |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/downstream-2/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_combustivel": 25,
    "consumo_mensal": 8000.0,
    "data": "2024-08-15",
    "unidade": "l",
    "cnpj": "00.000.000/0001-91"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo realizado com sucesso",
  "data": {
    "emissao_co2": 18.2,
    "emissao_ch4": 0.022,
    "emissao_n2o": 0.007,
    "emissao_total": 20.1,
    "emissao_co2_biogenico": 0.0,
    "metadados": {
      "combustivel": {
        "id": 25,
        "nome": "Gasolina Automotiva"
      },
      "unidade": "l",
      "data_referencia": "2024-08-15"
    }
  }
}
```

---

### **2. POST /api/downstream-2**

Calcula e persiste emissões de GEE para downstream 2 **com persistência no banco**.

#### **Requisição:**
```json
{
  "id_combustivel": 25,
  "consumo_mensal": 8000.0,
  "data": "2024-08-15",
  "unidade": "l",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "nvl_unidade_negocio": 2,
  "descricao": "Distribuição de gasolina para postos revendedores",
  "cnpj_fornecedor": "12.345.678/0001-90",
  "link_referencia": "http://exemplo.com/nota-fiscal-456",
  "id_usuario": "550e8400-e29b-41d4-a716-446655440000",
  "cnpj_usuario": ""
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_combustivel` | número | ID do combustível | 25 |
| `data` | string | Data de referência | "2024-08-15" |
| `unidade` | string | Unidade de medida (`"l"` ou `"m3"`) | "l" |
| `id_empresa` | string | ID da empresa | "dd68b58c-9155-42df-97ba-8ec86953c360" |
| `id_unidade_negocio` | string | ID da unidade de negócio | "680a4fe4-d2f1-4f2d-ab99-556effb557c9" |
| `nvl_unidade_negocio` | número | Nível hierárquico da unidade (1–10) | 2 |

#### **Parâmetros de Consumo (pelo menos um obrigatório):**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `consumo_mensal` | número | Consumo no mês |
| `consumo_anual` | número | Consumo anual |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `descricao` | string | Descrição adicional da operação |
| `cnpj_fornecedor` | string | CNPJ do fornecedor de combustível |
| `link_referencia` | string | Link para documento de referência (nota fiscal, contrato) |
| `id_usuario` | string | ID do usuário responsável pelo lançamento |
| `cnpj_usuario` | string | CNPJ do usuário (para casos específicos) |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/downstream-2" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_combustivel": 25,
    "consumo_mensal": 8000.0,
    "data": "2024-08-15",
    "unidade": "l",
    "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
    "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
    "nvl_unidade_negocio": 2
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo e persistência realizados",
  "data": {
    "resultado_calculo": {
      "emissao_co2": 18.2,
      "emissao_ch4": 0.022,
      "emissao_n2o": 0.007,
      "emissao_total": 20.1,
      "emissao_co2_biogenico": 0.0
    },
    "ids_gerados": {
      "documento_id": "550e8400-e29b-41d4-a716-446655440000",
      "entrada_id": 1234,
      "emissao_id": 5678
    },
    "metadados": {
      "empresa": "Empresa Exemplo Ltda",
      "unidade": "Filial São Paulo",
      "combustivel": "Gasolina Automotiva",
      "data": "2024-08-15"
    }
  }
}
```

#### **Notas Importantes:**
- O endpoint executa **validação de auditoria** automaticamente (decorator `@require_audit_validation`).
- Necessária permissão `calcular:escopo3`.
- A empresa e unidade devem existir no banco de dados.
- A versão GHG é obtida automaticamente do CNPJ da empresa.
- Combustíveis especiais (IDs 59, 60, 61 – Colômbia) são tratados sem validação na tabela de combustíveis compostos.
- Os dados de entrada são salvos na tabela específica `downstream_2` (consumo, unidade, combustível).

---

## **Endpoints de Consulta**

### **1. GET /api/downstream-2/unidade**

Consulta emissões de downstream 2 por unidade de negócio.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `id_unidade` | ✓ | string | ID da unidade |
| `nivel` | ✓ | número | Nível hierárquico da unidade (1–10) |
| `data_inicio` | ✗ | string | Data inicial (YYYY-MM-DD) |
| `data_fim` | ✗ | string | Data final (YYYY-MM-DD) |
| `incluir_descendentes` | ✗ | boolean | Incluir unidades filhas (padrão: true) |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/downstream-2/unidade?id_unidade=680a4fe4-d2f1-4f2d-ab99-556effb557c9&nivel=2&data_inicio=2024-01-01&data_fim=2024-12-31" \
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
    "emissao_total": 15800.5,
    "quantidade_documentos": 12,
    "quantidade_unidades": 3,
    "detalhes": [
      {
        "documento_id": "550e8400-e29b-41d4-a716-446655440000",
        "data": "2024-08-15",
        "descricao": "Downstream 2 - Combustível ID: 25",
        "unidade": {
          "id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
          "nome": "Filial São Paulo",
          "nivel": 2
        },
        "combustivel": {
          "id": 25,
          "nome": "Gasolina Automotiva"
        },
        "emissoes": {
          "co2": 18.2,
          "ch4": 0.022,
          "n2o": 0.007,
          "total": 20.1,
          "biogenica": 0.0
        },
        "created_at": "2024-08-15T11:20:00Z"
      }
    ],
    "filtros": {
      "data_inicio": "2024-01-01",
      "data_fim": "2024-12-31",
      "incluir_descendentes": true
    }
  }
}
```

---

### **2. GET /api/downstream-2/{documento_id}/detalhes**

Retorna detalhes completos de um documento específico de downstream 2.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path parameter) |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/downstream-2/550e8400-e29b-41d4-a716-446655440000/detalhes" \
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
      "data": "2024-08-15",
      "descricao": "Downstream 2 - Combustível ID: 25",
      "cnpj_fornecedor": "12.345.678/0001-90",
      "link_referencia": "http://exemplo.com/nota-fiscal-456",
      "id_categoria": 6,
      "id_usuario": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-08-15T11:20:00Z"
    },
    "entrada_downstream2": {
      "id": 1234,
      "id_combustivel": 25,
      "consumo": 8000.0,
      "unidade": "l",
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-08-15T11:20:00Z"
    },
    "combustivel": {
      "id": 25,
      "nome": "Gasolina Automotiva",
      "fossil": "Sim",
      "biocombustivel": "Não"
    },
    "emissoes": {
      "id": 5678,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "emissao_co2": 18.2,
      "emissao_ch4": 0.022,
      "emissao_n2o": 0.007,
      "emissao_total": 20.1,
      "emissao_biogenica": 0.0,
      "created_at": "2024-08-15T11:20:00Z"
    }
  }
}
```

---

### **3. GET /api/downstream-2/combustiveis**

Lista todos os combustíveis disponíveis para downstream 2 (tabela `lista_combustivel_composto`).

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/downstream-2/combustiveis" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": [
    {
      "id": 25,
      "combustivel": "Gasolina Automotiva",
      "fossil": "Sim",
      "biocombustivel": "Não",
      "fatores_emissao": {
        "co2": 0.002275,
        "ch4": 0.00000275,
        "n2o": 0.000000875,
        "co2_bio": 0.0
      }
    },
    {
      "id": 26,
      "combustivel": "Etanol Hidratado",
      "fossil": "Não",
      "biocombustivel": "Sim",
      "fatores_emissao": { ... }
    }
  ],
  "total": 35
}
```

---

### **4. GET /api/downstream-2/hierarquia-completa**

Retorna a hierarquia completa de emissões de downstream 2 para uma empresa, com totais por unidade.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `id_empresa` | ✓ | string | ID da empresa |
| `nivel_inicio` | ✗ | número | Nível hierárquico inicial (padrão: 1) |
| `ano` | ✗ | string | Ano para filtrar (YYYY) |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/downstream-2/hierarquia-completa?id_empresa=dd68b58c-9155-42df-97ba-8ec86953c360&nivel_inicio=1&ano=2024" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "empresa": {
      "id": "dd68b58c-9155-42df-97ba-8ec86953c360",
      "nome": "Empresa Exemplo Ltda",
      "cnpj": "00.000.000/0001-91"
    },
    "emissao_total_empresa": 62300.8,
    "nivel_inicio": 1,
    "quantidade_unidades_raiz": 2,
    "periodo": {
      "ano": "2024",
      "filtro_aplicado": true
    },
    "hierarquia": [
      {
        "id": "unidade-1-uuid",
        "nivel": 1,
        "nome": "Matriz São Paulo",
        "emissao_direta": 18000.0,
        "emissao_total": 41200.0,
        "filhas": [
          {
            "id": "unidade-2-uuid",
            "nivel": 2,
            "nome": "Filial Campinas",
            "emissao_direta": 12500.0,
            "emissao_total": 23200.0,
            "filhas": [
              {
                "id": "unidade-3-uuid",
                "nivel": 3,
                "nome": "Base Campinas",
                "emissao_direta": 10700.0,
                "emissao_total": 10700.0,
                "filhas": []
              }
            ]
          }
        ]
      },
      {
        "id": "unidade-4-uuid",
        "nivel": 1,
        "nome": "Filial Rio de Janeiro",
        "emissao_direta": 12500.0,
        "emissao_total": 21100.8,
        "filhas": [...]
      }
    ]
  }
}
```

---

## **Autenticação e Permissões**

Todos os endpoints requerem autenticação via API Key e permissões específicas:

| Endpoint | Método | Permissão Requerida |
|----------|--------|---------------------|
| `/downstream-2/calcular` | POST | `calcular:escopo3` |
| `/downstream-2` (persistência) | POST | `calcular:escopo3` |
| `/downstream-2/unidade` | GET | `consultar:resultados` |
| `/downstream-2/{documento_id}/detalhes` | GET | `consultar:resultados` |
| `/downstream-2/combustiveis` | GET | `consultar:resultados` |
| `/downstream-2/hierarquia-completa` | GET | `consultar:resultados` |

### **Headers de Autenticação:**
```http
X-API-Key: sua-chave-api-aqui
```

---

## **Regras de Cálculo**

### **Versão GHG**
- A versão GHG é obtida automaticamente do CNPJ da empresa.
- Essa versão determina quais fatores de emissão serão aplicados para cada tipo de combustível.

### **Fluxo de Cálculo:**
1. **Obter versão GHG** a partir do CNPJ.
2. **Identificar combustível** na tabela `lista_combustivel_composto` com os fatores de emissão correspondentes à versão GHG.  
   *Exceção:* combustíveis com IDs 59, 60 e 61 (Colômbia) não passam por essa validação e são tratados internamente.
3. **Validar unidade** – apenas `"l"` (litros) ou `"m3"` (metros cúbicos) são aceitos.
4. **Calcular emissões**:
   - Emissão CO₂ = Consumo × Fator CO₂ (versão específica)
   - Emissão CH₄ = Consumo × Fator CH₄
   - Emissão N₂O = Consumo × Fator N₂O
   - Emissão Total = Σ(Emissões × GWP correspondente)
   - Emissões biogênicas (CO₂ biogênico) calculadas apenas para biocombustíveis.
5. **Consumo**: se ambos `consumo_mensal` e `consumo_anual` forem fornecidos, o **anual** é utilizado (conforme lógica do `calcular_downstream_2`).

### **Validações:**
1. **CNPJ obrigatório** para todos os cálculos (persistente ou não).
2. **Versão GHG** deve existir para o CNPJ fornecido.
3. **Combustível** deve existir na base (exceto IDs especiais).
4. **Unidade** deve ser `"l"` ou `"m3"`.
5. **Consumo** deve ser maior que zero (mensal ou anual).
6. **Empresa/Unidade** devem existir no banco para persistência.
7. **Auditoria** automática para cálculos persistentes (decorator `@require_audit_validation`).

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Campos obrigatórios ausentes ou inválidos (ex.: consumo não informado, unidade inválida) | Verifique o corpo da requisição |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Necessário ter permissão `calcular:escopo3` ou `consultar:resultados` |
| `404` | Recurso não encontrado (empresa, unidade, combustível) | Verifique os IDs fornecidos |
| `500` | Erro interno do servidor | Consulte os logs do sistema |

---

## **Exemplos de Erro Comuns**

### **CNPJ sem versão GHG:**
```json
{
  "error": "Versão GHG não encontrada para o CNPJ: 00.000.000/0001-91"
}
```

### **Combustível não encontrado:**
```json
{
  "error": "Combustível ID 999 não encontrado"
}
```

### **Unidade inválida:**
```json
{
  "error": "Unidade deve ser 'l' ou 'm3'"
}
```

### **Nível de unidade inválido:**
```json
{
  "error": "nvl_unidade_negocio deve estar entre 1 e 10"
}
```

### **Consumo não informado:**
```json
{
  "error": "Forneça consumo_mensal ou consumo_anual"
}
```

### **Permissão insuficiente:**
```json
{
  "error": "Permissão 'calcular:escopo3' é necessária"
}
```

---

## **Observações Importantes**

1. **Dois modos de cálculo:** Use `/calcular` para simulações ou validações rápidas, use `/downstream-2` (sem o sufixo) para persistência oficial com auditoria.
2. **Versão GHG dinâmica:** Os fatores de emissão variam conforme a versão GHG vigente para a empresa (obtida pelo CNPJ).
3. **Combustíveis especiais:** Os IDs 59, 60 e 61 referem-se a combustíveis do contexto colombiano e são tratados sem validação na tabela de combustíveis compostos.
4. **Unidades permitidas:** Exclusivamente `"l"` (litros) e `"m3"` (metros cúbicos). Outras unidades resultarão em erro.
5. **Consumo mensal vs. anual:** Se ambos forem fornecidos, o **anual** tem precedência. O consumo armazenado na tabela específica será o valor utilizado (mensal ou anual).
6. **Hierarquia flexível:** As consultas por unidade permitem incluir ou não descendentes; o relatório de hierarquia completa mostra a árvore completa de emissões.
7. **Auditoria automática:** Todos os cálculos persistentes passam por validação de auditoria (ex.: se o documento já foi auditado, não pode ser alterado).
8. **Metadados enriquecidos:** As respostas incluem metadados sobre combustível, unidade, empresa, etc., para rastreabilidade.

---

