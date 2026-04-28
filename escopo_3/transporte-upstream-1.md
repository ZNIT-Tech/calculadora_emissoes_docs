# **Upstream 1 (Transporte e Distribuição) – Escopo 3**

Permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes do transporte e distribuição a montante (*upstream*) – etapa da cadeia de valor após a venda do produto.  
Este módulo contempla veículos e equipamentos utilizados na distribuição dos produtos vendidos pela empresa.

---

## **Endpoints de Cálculo**

### **1. POST /api/upstream-1/calcular**

Calcula emissões de GEE para uma frota/veículo **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "id_veiculo": 12,
  "ano_veiculo": 2020,
  "consumo_mensal": 5000.0,
  "consumo_anual": 0,
  "data": "2024-07-10",
  "cnpj": "00.000.000/0001-91",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_veiculo` | número | ID do veículo/equipamento (tabela `lista_veiculos_combustiveis`) | 12 |
| `ano_veiculo` | número | Ano de fabricação do veículo (para fator de correção, se aplicável) | 2020 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-10" |
| `cnpj` | string | CNPJ da empresa para obtenção da versão GHG | "00.000.000/0001-91" |

#### **Parâmetros de Consumo (pelo menos um obrigatório):**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `consumo_mensal` | número | Consumo no mês (em litros, kg ou m³, conforme unidade do veículo) |
| `consumo_anual` | número | Consumo anual (se fornecido, substitui o mensal para cálculo anual) |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_empresa` | string | ID da empresa (para metadados) |
| `id_unidade_negocio` | string | ID da unidade de negócio (para metadados) |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/upstream-1/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_veiculo": 12,
    "ano_veiculo": 2020,
    "consumo_mensal": 5000.0,
    "data": "2024-07-10",
    "cnpj": "00.000.000/0001-91"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo realizado com sucesso",
  "data": {
    "emissao_co2": 12.5,
    "emissao_ch4": 0.015,
    "emissao_n2o": 0.005,
    "emissao_total": 13.75,
    "emissao_co2_biogenico": 0.0,
    "metadados": {
      "veiculo": {
        "id": 12,
        "nome": "Caminhão Semipesado Diesel",
        "fossil": "Sim",
        "biocombustivel": "Não"
      },
      "ano_veiculo": 2020,
      "data_referencia": "2024-07-10"
    }
  }
}
```

---

### **2. POST /api/upstream-1**

Calcula e persiste emissões de GEE para upstream 1 **com persistência no banco**.

#### **Requisição:**
```json
{
  "id_veiculo": 12,
  "ano_veiculo": 2020,
  "consumo_mensal": 5000.0,
  "data": "2024-07-10",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "nvl_unidade_negocio": 2,
  "descricao": "Transporte de produtos para centro de distribuição",
  "cnpj_fornecedor": "12.345.678/0001-90",
  "link_referencia": "http://exemplo.com/nota-fiscal-123",
  "id_usuario": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_veiculo` | número | ID do veículo | 12 |
| `ano_veiculo` | número | Ano de fabricação | 2020 |
| `data` | string | Data de referência | "2024-07-10" |
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
| `cnpj_fornecedor` | string | CNPJ do fornecedor de combustível (se aplicável) |
| `link_referencia` | string | Link para documento de referência (nota fiscal, relatório) |
| `id_usuario` | string | ID do usuário responsável pelo lançamento |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/upstream-1" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_veiculo": 12,
    "ano_veiculo": 2020,
    "consumo_mensal": 5000.0,
    "data": "2024-07-10",
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
      "emissao_co2": 12.5,
      "emissao_ch4": 0.015,
      "emissao_n2o": 0.005,
      "emissao_total": 13.75,
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
      "veiculo": "Caminhão Semipesado Diesel",
      "data": "2024-07-10"
    }
  }
}
```

#### **Notas Importantes:**
- Este endpoint executa **validação de auditoria** automaticamente (decorator `@require_audit_validation`).
- Necessária permissão `calcular:escopo3`.
- A empresa e unidade devem existir no banco de dados.
- A versão GHG é obtida automaticamente do CNPJ da empresa.
- Os dados de entrada são salvos na tabela específica `upstream_1` (consumo mensal/anual, veículo, etc.).

---

## **Endpoints de Consulta**

### **1. GET /api/upstream-1/unidade**

Consulta emissões de upstream 1 por unidade de negócio.

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
curl "http://localhost:8080/api/upstream-1/unidade?id_unidade=680a4fe4-d2f1-4f2d-ab99-556effb557c9&nivel=2&data_inicio=2024-01-01&data_fim=2024-12-31" \
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
    "emissao_total": 12500.75,
    "quantidade_documentos": 8,
    "quantidade_unidades": 3,
    "detalhes": [
      {
        "documento_id": "550e8400-e29b-41d4-a716-446655440000",
        "data": "2024-07-10",
        "descricao": "Upstream 1 - Veículo: Caminhão Semipesado Diesel",
        "unidade": {
          "id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
          "nome": "Filial São Paulo",
          "nivel": 2
        },
        "veiculo": {
          "id": 12,
          "nome": "Caminhão Semipesado Diesel"
        },
        "emissoes": {
          "co2": 12.5,
          "ch4": 0.015,
          "n2o": 0.005,
          "total": 13.75,
          "biogenica": 0.0
        },
        "created_at": "2024-07-10T14:30:00Z"
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

### **2. GET /api/upstream-1/{documento_id}/detalhes**

Retorna detalhes completos de um documento específico de upstream 1.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path parameter) |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/upstream-1/550e8400-e29b-41d4-a716-446655440000/detalhes" \
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
      "data": "2024-07-10",
      "descricao": "Upstream 1 - Veículo: Caminhão Semipesado Diesel",
      "cnpj_fornecedor": "12.345.678/0001-90",
      "link_referencia": "http://exemplo.com/nota-fiscal-123",
      "id_categoria": 5,
      "id_usuario": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-10T14:30:00Z"
    },
    "entrada_upstream1": {
      "id": 1234,
      "id_veiculo": 12,
      "ano_veiculo": 2020,
      "consumo_mensal": 5000.0,
      "consumo_anual": null,
      "data": "2024-07-10",
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-10T14:30:00Z"
    },
    "veiculo": {
      "id": 12,
      "nome": "Caminhão Semipesado Diesel",
      "fossil": "Sim",
      "biocombustivel": "Não"
    },
    "emissoes": {
      "id": 5678,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "emissao_co2": 12.5,
      "emissao_ch4": 0.015,
      "emissao_n2o": 0.005,
      "emissao_total": 13.75,
      "emissao_biogenica": 0.0,
      "created_at": "2024-07-10T14:30:00Z"
    }
  }
}
```

---

### **3. GET /api/upstream-1/veiculos**

Lista todos os veículos/equipamentos disponíveis para upstream 1 (tabela `lista_veiculos_combustiveis`).

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/upstream-1/veiculos" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": [
    {
      "id": 12,
      "veiculo": "Caminhão Semipesado Diesel",
      "fossil": "Sim",
      "biocombustivel": "Não",
      "fatores_emissao": {
        "co2": 0.0025,
        "ch4": 0.000003,
        "n2o": 0.000001,
        "co2_bio": 0.0,
        "ch4_bio": 0.0,
        "n2o_bio": 0.0
      }
    },
    {
      "id": 13,
      "veiculo": "Van Gasolina",
      "fossil": "Sim",
      "biocombustivel": "Não",
      "fatores_emissao": { ... }
    }
  ],
  "total": 25
}
```

---

### **4. GET /api/upstream-1/hierarquia-completa**

Retorna a hierarquia completa de emissões de upstream 1 para uma empresa, com totais por unidade.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `id_empresa` | ✓ | string | ID da empresa |
| `nivel_inicio` | ✗ | número | Nível hierárquico inicial (padrão: 1) |
| `ano` | ✗ | string | Ano para filtrar (YYYY) |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/upstream-1/hierarquia-completa?id_empresa=dd68b58c-9155-42df-97ba-8ec86953c360&nivel_inicio=1&ano=2024" \
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
    "emissao_total_empresa": 48500.3,
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
        "emissao_direta": 12000.0,
        "emissao_total": 31200.0,
        "filhas": [
          {
            "id": "unidade-2-uuid",
            "nivel": 2,
            "nome": "Filial Campinas",
            "emissao_direta": 8500.0,
            "emissao_total": 19200.0,
            "filhas": [
              {
                "id": "unidade-3-uuid",
                "nivel": 3,
                "nome": "Depósito Campinas",
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
        "emissao_direta": 9500.0,
        "emissao_total": 17300.0,
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
| `/downstream-1/calcular` | POST | `calcular:escopo3` |
| `/downstream-1` (persistência) | POST | `calcular:escopo3` |
| `/downstream-1/unidade` | GET | `consultar:resultados` |
| `/downstream-1/{documento_id}/detalhes` | GET | `consultar:resultados` |
| `/downstream-1/veiculos` | GET | `consultar:resultados` |
| `/downstream-1/hierarquia-completa` | GET | `consultar:resultados` |

### **Headers de Autenticação:**
```http
X-API-Key: sua-chave-api-aqui
```

---

## **Regras de Cálculo**

### **Versão GHG**
- A versão GHG é obtida automaticamente do CNPJ da empresa.
- Essa versão determina quais fatores de emissão serão aplicados para cada tipo de veículo/combustível.

### **Fluxo de Cálculo:**
1. **Obter versão GHG** a partir do CNPJ.
2. **Identificar veículo** na tabela `lista_veiculos_combustiveis` com os fatores de emissão correspondentes à versão GHG.
3. **Ajustar por ano do veículo** (se aplicável, conforme metodologia GHG).
4. **Calcular emissões**:
   - Emissão CO₂ = Consumo × Fator CO₂ (versão específica)
   - Emissão CH₄ = Consumo × Fator CH₄
   - Emissão N₂O = Consumo × Fator N₂O
   - Emissão Total = Σ(Emissões × GWP correspondente)
   - Emissões biogênicas (CO₂ biogênico) calculadas apenas para biocombustíveis.

### **Validações:**
1. **CNPJ obrigatório** para todos os cálculos (persistente ou não).
2. **Versão GHG** deve existir para o CNPJ fornecido.
3. **Veículo** deve existir e estar ativo para a versão GHG.
4. **Empresa/Unidade** devem existir no banco para persistência.
5. **Auditoria** automática para cálculos persistentes (decorator `@require_audit_validation`).

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Campos obrigatórios ausentes ou inválidos (ex.: consumo não informado) | Verifique o corpo da requisição |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Necessário ter permissão `calcular:escopo3` ou `consultar:resultados` |
| `404` | Recurso não encontrado (empresa, unidade, veículo) | Verifique os IDs fornecidos |
| `500` | Erro interno do servidor | Consulte os logs do sistema |

---

## **Exemplos de Erro Comuns**

### **CNPJ sem versão GHG:**
```json
{
  "error": "Versão GHG não encontrada para o CNPJ: 00.000.000/0001-91"
}
```

### **Veículo não encontrado:**
```json
{
  "error": "Veículo ID 999 não encontrado"
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

1. **Dois modos de cálculo:** Use `/calcular` para simulações ou validações rápidas, use `/downstream-1` (sem o sufixo) para persistência oficial com auditoria.
2. **Versão GHG dinâmica:** Os fatores de emissão variam conforme a versão GHG vigente para a empresa (obtida pelo CNPJ).
3. **Consumo mensal vs. anual:** O cálculo pode ser baseado em consumo mensal ou anual. Se ambos forem fornecidos, o **anual** tem precedência (segundo a lógica atual, mas verifique se a implementação permite apenas um deles; a documentação indica que pelo menos um deve ser >0).
4. **Hierarquia flexível:** As consultas por unidade permitem incluir ou não descendentes; o relatório de hierarquia completa mostra a árvore completa de emissões.
5. **Auditoria automática:** Todos os cálculos persistentes passam por validação de auditoria (ex.: se o documento já foi auditado, não pode ser alterado).
6. **Metadados enriquecidos:** As respostas incluem metadados sobre veículo, unidade, empresa, etc., para rastreabilidade.

