Aqui está a documentação completa para o módulo **Casa Trabalho 2**, seguindo o mesmo padrão da documentação de Casa Trabalho 1 e incluindo os curls de exemplo para todos os endpoints (consulta e cálculo) utilizando a mesma empresa e unidade de negócio fornecidas anteriormente.

---

# **Casa Trabalho 2 (Escopo 3)**

Este módulo permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes de **deslocamentos casa-trabalho** baseados no **consumo de combustível** (categoria 2). Diferentemente da categoria 1, aqui o veículo não é especificado; utiliza-se diretamente o combustível consumido (ex: gasolina, diesel, etanol) e seus fatores de emissão, considerando os percentuais mensais de mistura (etanol na gasolina, biodiesel no diesel).

---

## **Endpoints de Cálculo**

### **1. POST /api/casa-trabalho-2/calcular**

Calcula emissões de GEE para um deslocamento casa-trabalho **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "id_combustivel": 3,
  "consumo_medio_dia": 2.5,
  "consumo_mensal": 22,
  "consumo_anual": 0,
  "data": "2024-01-15",
  "ida_volta": true,
  "cnpj": "12.345.678/0001-90"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_combustivel` | número | ID do combustível (tabela `lista_combustiveis_compostos`) | 3 |
| `consumo_medio_dia` | número | Consumo médio diário (litros/dia) | 2.5 |
| `data` | string | Data de referência (YYYY-MM-DD) para percentuais de mistura | "2024-01-15" |
| `ida_volta` | boolean | Se `true`, considera ida e volta (dobra as emissões) | true |
| `cnpj` | string | CNPJ da empresa (para obter a versão GHG) | "12.345.678/0001-90" |

**Obrigatório (pelo menos um):**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `consumo_mensal` | número | Dias trabalhados no mês (ou consumo mensal) |
| `consumo_anual` | número | Dias trabalhados no ano (ou consumo anual) |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_empresa` | string | UUID da empresa (metadados) |
| `id_unidade_negocio` | string | UUID da unidade de negócio (metadados) |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-2/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q" \
  -d '{
    "id_combustivel": 3,
    "consumo_medio_dia": 2.5,
    "consumo_mensal": 22,
    "consumo_anual": 0,
    "data": "2024-01-15",
    "ida_volta": true,
    "cnpj": "12.345.678/0001-90",
    "id_empresa": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
    "id_unidade_negocio": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo realizado com sucesso",
  "data": {
    "consumo_fossil": 40.15,
    "consumo_biocombustivel": 14.85,
    "fator_co2": 2.2,
    "fator_ch4": 0.1,
    "fator_n2o": 0.02,
    "emissao_co2": 0.08833,
    "emissao_ch4": 0.0047575,
    "emissao_n2o": 0.0009515,
    "emissao_total": 0.47369,
    "emissao_biogenica": 0.022275,
    "combustivel_fossil_utilizado": "Gasolina Automotiva (pura)",
    "biocombustivel_utilizado": "Etanol Anidro",
    "percentual_bio": 0.27,
    "versao_ghg": "v2025.0.1",
    "metadados": {
      "combustivel": {
        "id": 3,
        "nome": "Gasolina C",
        "fossil": "Gasolina Automotiva (pura)",
        "biocombustivel": "Etanol Anidro"
      },
      "consumo_medio_dia": 2.5,
      "ida_volta": true,
      "data_referencia": "2024-01-15"
    }
  }
}
```

---

### **2. POST /api/casa-trabalho-2**

Calcula e persiste emissões de GEE para um deslocamento casa-trabalho **com persistência no banco** (cria documento, registro na tabela específica e emissões).

#### **Requisição:**
```json
{
  "id_combustivel": 3,
  "consumo_medio_dia": 2.5,
  "consumo_mensal": 22,
  "consumo_anual": 0,
  "data": "2024-01-15",
  "ida_volta": true,
  "id_empresa": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
  "id_unidade_negocio": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a",
  "nvl_unidade_negocio": 2,
  "descricao": "Deslocamento diário - combustível misto",
  "cnpj_fornecedor": "12.345.678/0001-90",
  "link_referencia": "http://exemplo.com/planilha",
  "id_usuario": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_combustivel` | número | ID do combustível |
| `consumo_medio_dia` | número | Consumo médio diário |
| `data` | string | Data de referência |
| `ida_volta` | boolean | Ida e volta? |
| `id_empresa` | string | UUID da empresa |
| `id_unidade_negocio` | string | UUID da unidade de negócio |
| `nvl_unidade_negocio` | número | Nível hierárquico (1–10) |

**Obrigatório (pelo menos um):**
- `consumo_mensal` ou `consumo_anual`

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_usuario` | string | UUID do usuário responsável |
| `descricao` | string | Descrição adicional |
| `cnpj_fornecedor` | string | CNPJ do fornecedor (se aplicável) |
| `link_referencia` | string | Link para documento comprobatório |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-2" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q" \
  -d '{
    "id_combustivel": 3,
    "consumo_medio_dia": 2.5,
    "consumo_mensal": 22,
    "consumo_anual": 0,
    "data": "2024-01-15",
    "ida_volta": true,
    "id_empresa": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
    "id_unidade_negocio": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a",
    "nvl_unidade_negocio": 2,
    "descricao": "Deslocamento diário - combustível misto"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo e persistência realizados",
  "data": {
    "resultado_calculo": {
      "consumo_fossil": 40.15,
      "consumo_biocombustivel": 14.85,
      "emissao_total": 0.47369,
      "emissao_biogenica": 0.022275,
      ...
    },
    "ids_gerados": {
      "documento_id": "550e8400-e29b-41d4-a716-446655440000",
      "entrada_id": 123,
      "emissao_id": 456
    },
    "metadados": {
      "empresa": "Empresa Exemplo",
      "unidade": "Filial São Paulo",
      "combustivel": "Gasolina C",
      "data": "2024-01-15"
    }
  }
}
```

---

## **Endpoints de Consulta**

### **1. GET /api/casa-trabalho-2/unidade**

Consulta emissões de casa trabalho 2 agregadas por unidade de negócio, com possibilidade de incluir unidades descendentes.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_unidade` | ✓ | string | UUID da unidade | — |
| `nivel` | ✓ | número | Nível hierárquico (1–10) | — |
| `data_inicio` | ✗ | string | Data inicial (YYYY-MM-DD) | — |
| `data_fim` | ✗ | string | Data final (YYYY-MM-DD) | — |
| `incluir_descendentes` | ✗ | boolean | Incluir unidades filhas | `true` |

#### **Exemplo de Uso:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-2/unidade?id_unidade=1b3e142c-a8f2-4fe4-a0eb-3b138967f03a&nivel=2&incluir_descendentes=true&data_inicio=2024-01-01&data_fim=2024-12-31" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q"
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "data": {
    "unidade": {
      "id": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a",
      "nome": "Filial São Paulo",
      "nivel": 2
    },
    "emissao_total": 2.345,
    "quantidade_documentos": 4,
    "quantidade_unidades": 3,
    "detalhes": [
      {
        "documento_id": "550e8400-e29b-41d4-a716-446655440000",
        "data": "2024-01-15",
        "descricao": "Casa Trabalho 2 - Combustível: Gasolina C",
        "unidade": {
          "id": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a",
          "nome": "Filial São Paulo",
          "nivel": 2
        },
        "emissoes": {
          "co2": 0.08833,
          "ch4": 0.0047575,
          "n2o": 0.0009515,
          "total": 0.47369,
          "biogenica": 0.022275
        },
        "created_at": "2024-01-15T10:30:00Z"
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

### **2. GET /api/casa-trabalho-2/{documento_id}/detalhes**

Consulta detalhes completos de um documento específico de casa trabalho 2.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path) |

#### **Exemplo de Uso:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-2/550e8400-e29b-41d4-a716-446655440000/detalhes" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q"
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "data": {
    "documento": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "id_empresa": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
      "id_unidade_negocio": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a",
      "nvl_unidade_negocio": 2,
      "data": "2024-01-15",
      "descricao": "Casa Trabalho 2 - Combustível: Gasolina C",
      "link_referencia": null,
      "id_categoria": 9,
      "id_usuario": null,
      "created_at": "2024-01-15T10:30:00Z"
    },
    "entrada": {
      "id": 123,
      "id_combustivel": 3,
      "dias_trabalhados_mes": 22,
      "dias_trabalhados_ano": null,
      "consumo_medio_dia": 2.5,
      "is_ida_volta": true,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-01-15T10:30:00Z",
      "lista_combustiveis_compostos": {
        "id": 3,
        "combustivel": "Gasolina C",
        "fossil": "Gasolina Automotiva (pura)",
        "biocombustivel": "Etanol Anidro",
        "co2_fossil": 2.2,
        "co2_bio": 1.5,
        "ch4_fossil": 0.1,
        "ch4_bio": 0.05,
        "n2o_fossil": 0.02,
        "n2o_bio": 0.01
      }
    },
    "emissoes": {
      "id": 456,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "emissao_co2": 0.08833,
      "emissao_ch4": 0.0047575,
      "emissao_n2o": 0.0009515,
      "emissao_total": 0.47369,
      "emissao_biogenica": 0.022275,
      "created_at": "2024-01-15T10:30:00Z"
    }
  }
}
```

---

### **3. GET /api/casa-trabalho-2/combustiveis**

Lista todos os combustíveis disponíveis para cálculo (tabela `lista_combustiveis_compostos`).

#### **Exemplo de Uso:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-2/combustiveis" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q"
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "combustivel": "Gasolina Automotiva (pura)",
      "fossil": "Gasolina Automotiva (pura)",
      "biocombustivel": "-",
      "co2_fossil": 2.2,
      "co2_bio": 0,
      "ch4_fossil": 0.1,
      "ch4_bio": 0,
      "n2o_fossil": 0.02,
      "n2o_bio": 0,
      "versao_ghg": "v2025.0.1"
    },
    {
      "id": 2,
      "combustivel": "Óleo Diesel (puro)",
      "fossil": "Óleo Diesel (puro)",
      "biocombustivel": "-",
      "co2_fossil": 2.6,
      "co2_bio": 0,
      "ch4_fossil": 0.15,
      "ch4_bio": 0,
      "n2o_fossil": 0.03,
      "n2o_bio": 0,
      "versao_ghg": "v2025.0.1"
    },
    {
      "id": 3,
      "combustivel": "Gasolina C",
      "fossil": "Gasolina Automotiva (pura)",
      "biocombustivel": "Etanol Anidro",
      "co2_fossil": 2.2,
      "co2_bio": 1.5,
      "ch4_fossil": 0.1,
      "ch4_bio": 0.05,
      "n2o_fossil": 0.02,
      "n2o_bio": 0.01,
      "versao_ghg": "v2025.0.1"
    }
  ],
  "total": 3
}
```

---

### **4. GET /api/casa-trabalho-2/hierarquia-completa**

Retorna a hierarquia completa de emissões de casa trabalho 2 para uma empresa, agregando por unidade de negócio e suas descendentes.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_empresa` | ✓ | string | UUID da empresa | — |
| `nivel_inicio` | ✗ | número | Nível hierárquico inicial (1–10) | 1 |
| `ano` | ✗ | string | Ano para filtrar (YYYY) | — |

#### **Exemplo de Uso:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-2/hierarquia-completa?id_empresa=bf725c29-2b8c-46dc-a3ad-880e511e7804&nivel_inicio=1&ano=2024" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q"
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "data": {
    "empresa": {
      "id": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
      "nome": "Empresa Exemplo",
      "cnpj": "12.345.678/0001-90"
    },
    "emissao_total_empresa": 28.94,
    "nivel_inicio": 1,
    "quantidade_unidades_raiz": 2,
    "periodo": {
      "ano": "2024",
      "filtro_aplicado": true
    },
    "hierarquia": [
      {
        "id": "matriz-uuid",
        "nivel": 1,
        "nome": "Matriz",
        "emissao_direta": 12.34,
        "emissao_total": 18.45,
        "filhas": [
          {
            "id": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a",
            "nivel": 2,
            "nome": "Filial São Paulo",
            "emissao_direta": 6.11,
            "emissao_total": 6.11,
            "filhas": []
          }
        ]
      },
      {
        "id": "filial-rj-uuid",
        "nivel": 1,
        "nome": "Filial Rio",
        "emissao_direta": 10.49,
        "emissao_total": 10.49,
        "filhas": []
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
| `/casa-trabalho-2/calcular` | POST | `calcular:escopo3` |
| `/casa-trabalho-2` (persistência) | POST | `calcular:escopo3` |
| `/casa-trabalho-2/unidade` | GET | `consultar:resultados` |
| `/casa-trabalho-2/{documento_id}/detalhes` | GET | `consultar:resultados` |
| `/casa-trabalho-2/combustiveis` | GET | `consultar:resultados` |
| `/casa-trabalho-2/hierarquia-completa` | GET | `consultar:resultados` |

**Header de Autenticação:**
```http
X-API-Key: sua-chave-api-aqui
```

---

## **Regras de Cálculo**

### **Metodologia**
O cálculo segue a abordagem de **consumo estimado** baseado em dias trabalhados:

```
Consumo total (L) = (dias mês OU dias ano) × consumo médio diário (L/dia)
```

Em seguida, o consumo total é dividido em fração fóssil e biocombustível com base nos percentuais mensais de mistura (etanol na gasolina, biodiesel no diesel), obtidos da tabela `lista_percentual_sin`. Para Casa Trabalho 2, utiliza-se **COALESCE**: prioriza o percentual de etanol; se não houver (ex.: diesel), usa o percentual de biodiesel.

As emissões são calculadas utilizando fatores de emissão da tabela `lista_combustiveis_compostos`:

- **CO₂ fóssil** (t) = co2_fossil (kg/L) × consumo_fóssil (L) ÷ 1000
- **CH₄** (t) = (ch4_fossil × consumo_fóssil + ch4_bio × consumo_bio) ÷ 1000
- **N₂O** (t) = (n2o_fossil × consumo_fóssil + n2o_bio × consumo_bio) ÷ 1000
- **CO₂ biogênico** (t) = co2_bio × consumo_bio ÷ 1000

A emissão total em **tCO₂e** é obtida aplicando os potenciais de aquecimento global (GWP) do IPCC AR5 (CH₄ = 28, N₂O = 265).

### **Fatores de Emissão**
- Os fatores são obtidos da tabela `lista_combustiveis_compostos` para o combustível específico e versão GHG da empresa.
- Os fatores estão em **kg de emissão por litro de combustível**.

### **Percentuais de Mistura**
- Os percentuais de etanol na gasolina e biodiesel no diesel são obtidos da tabela `lista_percentual_sin` para o mês e ano da `data` informada.
- Se não houver registro para o mês/ano, utiliza-se **0%** (combustível 100% fóssil) e um aviso é registrado no log.

### **Ida e Volta**
- Quando `ida_volta = true`, todos os valores de consumo e emissões são **dobrados**.

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Campos obrigatórios ausentes ou inválidos | Verifique o corpo da requisição |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Verifique suas permissões |
| `404` | Recurso não encontrado | Verifique IDs de combustível, empresa, unidade |
| `500` | Erro interno do servidor | Consulte os logs do sistema |

---

## **Exemplos de Erro Comuns**

### **Combustível não encontrado:**
```json
{
  "error": "Combustível ID 99 não encontrado para a versão GHG v2025.0.1"
}
```

### **Fatores não encontrados (campos zerados):**
```json
{
  "error": "Combustível não encontrado"
}
```

### **Empresa sem CNPJ:**
```json
{
  "error": "Versão GHG não encontrada para o CNPJ: 12.345.678/0001-90"
}
```

### **Nível da unidade inválido:**
```json
{
  "error": "nvl_unidade_negocio deve estar entre 1 e 10"
}
```

---

## **Observações Importantes**

1. **Dois modos de cálculo:** Utilize `/calcular` para testes e simulações; utilize o POST sem `/calcular` para lançamento oficial com persistência e auditoria.
2. **Dependência da versão GHG:** O módulo utiliza a versão GHG da empresa para buscar os fatores de emissão corretos. Certifique-se de que a empresa tenha a coluna `versao_ghg` preenchida.
3. **Tabela específica:** Os parâmetros de entrada são persistidos na tabela `cat_casa_trabalho_2`, com campos `dias_trabalhados_mes`, `dias_trabalhados_ano`, `consumo_medio_dia`, `is_ida_volta`.
4. **Categoria:** A categoria de Casa Trabalho 2 é obtida dinamicamente. Se não existir, o sistema retorna erro. Cadastre-a previamente na tabela `categorias_emissao` com `tabela = 'cat_casa_trabalho_2'`.
5. **Auditoria:** Todos os cálculos persistentes passam por validação de auditoria (`@require_audit_validation`), garantindo rastreabilidade.
6. **Hierarquia flexível:** A consulta de hierarquia permite definir o nível inicial e inclui recursivamente todas as unidades descendentes.
7. **Combustíveis disponíveis:** Consulte o endpoint `/combustiveis` para obter a lista completa de combustíveis cadastrados e seus fatores.