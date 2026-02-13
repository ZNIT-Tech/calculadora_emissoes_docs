# **Casa Trabalho 1**

Este módulo permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes de **deslocamentos casa-trabalho** realizados por veículos da empresa ou dos funcionários. A categoria **Casa Trabalho 1** corresponde ao uso de veículos leves (automóveis, motos) cujo consumo é estimado com base em dias trabalhados e consumo médio diário.

---

## **Endpoints de Cálculo**

### **1. POST /api/casa-trabalho-1/calcular**

Calcula emissões de GEE para um deslocamento casa-trabalho **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "id_veiculo": 5,
  "ano_frota": 2020,
  "consumo_mensal": 22,
  "consumo_anual": 0,
  "consumo_medio_dia": 2.5,
  "data": "2024-01-15",
  "ida_volta": true,
  "cnpj": "12.345.678/0001-90"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_veiculo` | número | ID do veículo (tabela `lista_veiculos_combustiveis`) | 5 |
| `ano_frota` | número | Ano de fabricação do veículo | 2020 |
| `consumo_medio_dia` | número | Consumo médio diário (litros/dia ou m³/dia) | 2.5 |
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
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-1/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_veiculo": 5,
    "ano_frota": 2020,
    "consumo_mensal": 22,
    "consumo_anual": 0,
    "consumo_medio_dia": 2.5,
    "data": "2024-01-15",
    "ida_volta": true,
    "cnpj": "12.345.678/0001-90"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo realizado com sucesso",
  "data": {
    "consumo_fossil": 80.3,
    "consumo_biocombustivel": 29.7,
    "fator_co2": 2.2,
    "fator_ch4": 0.1,
    "fator_n2o": 0.02,
    "emissao_co2": 0.17666,
    "emissao_ch4": 0.0055,
    "emissao_n2o": 0.0011,
    "emissao_total": 0.53383,
    "biocombustivel_utilizado": "Etanol Anidro",
    "combustivel_fossil_utilizado": "Gasolina Automotiva (pura)",
    "emissao_biogenica": 0.04455,
    "metadados": {
      "veiculo": {
        "id": 5,
        "nome": "Carro Flex",
        "combustivel_fossil": "Gasolina Automotiva (pura)",
        "biocombustivel": "Etanol Anidro"
      },
      "ano_frota": 2020,
      "consumo_medio_dia": 2.5,
      "ida_volta": true,
      "data_referencia": "2024-01-15"
    }
  }
}
```

---

### **2. POST /api/casa-trabalho-1**

Calcula e persiste emissões de GEE para um deslocamento casa-trabalho **com persistência no banco** (cria documento, registro na tabela específica e emissões).

#### **Requisição:**
```json
{
  "id_veiculo": 5,
  "ano_frota": 2020,
  "consumo_mensal": 22,
  "consumo_anual": 0,
  "consumo_medio_dia": 2.5,
  "data": "2024-01-15",
  "ida_volta": true,
  "id_empresa": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
  "id_unidade_negocio": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a",
  "nvl_unidade_negocio": 2,
  "descricao": "Deslocamento diário funcionários",
  "cnpj_fornecedor": "12.345.678/0001-90",
  "link_referencia": "http://exemplo.com/planilha",
  "id_usuario": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_veiculo` | número | ID do veículo |
| `ano_frota` | número | Ano do veículo |
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
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-1" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_veiculo": 5,
    "ano_frota": 2020,
    "consumo_mensal": 22,
    "consumo_anual": 0,
    "consumo_medio_dia": 2.5,
    "data": "2024-01-15",
    "ida_volta": true,
    "id_empresa": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
    "id_unidade_negocio": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a",
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
      "consumo_fossil": 80.3,
      "consumo_biocombustivel": 29.7,
      "emissao_total": 0.53383,
      "emissao_biogenica": 0.04455,
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
      "veiculo": "Carro Flex",
      "data": "2024-01-15"
    }
  }
}
```

---

## **Endpoints de Consulta**

### **1. GET /api/casa-trabalho-1/unidade**

Consulta emissões de casa trabalho 1 agregadas por unidade de negócio, com possibilidade de incluir unidades descendentes.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_unidade` | ✓ | string | UUID da unidade | — |
| `nivel` | ✓ | número | Nível hierárquico (1–10) | — |
| `data_inicio` | ✗ | string | Data inicial (YYYY-MM-DD) | — |
| `data_fim` | ✗ | string | Data final (YYYY-MM-DD) | — |
| `incluir_descendentes` | ✗ | boolean | Incluir unidades filhas | `true` |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-1/unidade?id_unidade=1b3e142c-a8f2-4fe4-a0eb-3b138967f03a&nivel=2&incluir_descendentes=true" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "unidade": {
      "id": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a",
      "nome": "Filial São Paulo",
      "nivel": 2
    },
    "emissao_total": 1.234,
    "quantidade_documentos": 3,
    "quantidade_unidades": 5,
    "detalhes": [
      {
        "documento_id": "550e8400-e29b-41d4-a716-446655440000",
        "data": "2024-01-15",
        "descricao": "Casa Trabalho 1 - Veículo: Carro Flex",
        "unidade": {
          "id": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a",
          "nome": "Filial São Paulo",
          "nivel": 2
        },
        "emissoes": {
          "co2": 0.17666,
          "ch4": 0.0055,
          "n2o": 0.0011,
          "total": 0.53383,
          "biogenica": 0.04455
        },
        "created_at": "2024-01-15T10:30:00Z"
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

### **2. GET /api/casa-trabalho-1/{documento_id}/detalhes**

Consulta detalhes completos de um documento específico de casa trabalho 1.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path) |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-1/550e8400-e29b-41d4-a716-446655440000/detalhes" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
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
      "descricao": "Casa Trabalho 1 - Veículo: Carro Flex",
      "link_referencia": null,
      "id_categoria": 8,
      "id_usuario": null,
      "created_at": "2024-01-15T10:30:00Z"
    },
    "entrada": {
      "id": 123,
      "id_veiculo": 5,
      "ano_frota": 2020,
      "dias_trabalhos_mes": 22,
      "dias_trabalhados_ano": null,
      "consumo_medio_dia": 2.5,
      "is_ida_volta": true,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-01-15T10:30:00Z",
      "lista_veiculos_combustiveis": {
        "id": 5,
        "veiculo": "Carro Flex",
        "fossil": "Gasolina Automotiva (pura)",
        "biocombustivel": "Etanol Anidro"
      }
    },
    "emissoes": {
      "id": 456,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "emissao_co2": 0.17666,
      "emissao_ch4": 0.0055,
      "emissao_n2o": 0.0011,
      "emissao_total": 0.53383,
      "emissao_biogenica": 0.04455,
      "created_at": "2024-01-15T10:30:00Z"
    }
  }
}
```

---

### **3. GET /api/casa-trabalho-1/veiculos**

Lista todos os veículos disponíveis para cálculo (tabela `lista_veiculos_combustiveis`).

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-1/veiculos" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "veiculo": "Carro Flex",
      "fossil": "Gasolina Automotiva (pura)",
      "biocombustivel": "Etanol Anidro",
      "versao_ghg": "v2025.0.1",
      "pais": "Brasil"
    },
    {
      "id": 2,
      "veiculo": "Caminhão Diesel",
      "fossil": "Óleo Diesel (puro)",
      "biocombustivel": "Biodiesel (B100)",
      "versao_ghg": "v2025.0.1",
      "pais": "Brasil"
    }
  ],
  "total": 2
}
```

---

### **4. GET /api/casa-trabalho-1/hierarquia-completa**

Retorna a hierarquia completa de emissões de casa trabalho 1 para uma empresa, agregando por unidade de negócio e suas descendentes.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_empresa` | ✓ | string | UUID da empresa | — |
| `nivel_inicio` | ✗ | número | Nível hierárquico inicial (1–10) | 1 |
| `ano` | ✗ | string | Ano para filtrar (YYYY) | — |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-1/hierarquia-completa?id_empresa=bf725c29-2b8c-46dc-a3ad-880e511e7804&nivel_inicio=1&ano=2024" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "empresa": {
      "id": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
      "nome": "Empresa Exemplo",
      "cnpj": "12.345.678/0001-90"
    },
    "emissao_total_empresa": 15.67,
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
        "nome": "Matriz",
        "emissao_direta": 5.23,
        "emissao_total": 10.45,
        "filhas": [
          {
            "id": "unidade-2-uuid",
            "nivel": 2,
            "nome": "Filial São Paulo",
            "emissao_direta": 3.12,
            "emissao_total": 5.22,
            "filhas": [...]
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
| `/casa-trabalho-1/calcular` | POST | `calcular:escopo3` |
| `/casa-trabalho-1` (persistência) | POST | `calcular:escopo3` |
| `/casa-trabalho-1/unidade` | GET | `consultar:resultados` |
| `/casa-trabalho-1/{documento_id}/detalhes` | GET | `consultar:resultados` |
| `/casa-trabalho-1/veiculos` | GET | `consultar:resultados` |
| `/casa-trabalho-1/hierarquia-completa` | GET | `consultar:resultados` |

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

Em seguida, o consumo total é dividido em fração fóssil e biocombustível com base nos percentuais mensais de mistura (etanol na gasolina, biodiesel no diesel), obtidos da tabela `lista_percentual_sin`.

As emissões são calculadas utilizando fatores de emissão específicos do veículo (obtidos da tabela `lista_emissao_geral` para o ano da frota):

- **CO₂ fóssil** (t) = fator_co2 (kg/L) × consumo_fóssil (L) ÷ 1000
- **CH₄** (t) = fator_ch4 (kg/L) × consumo_total (L) ÷ 1000
- **N₂O** (t) = fator_n2o (kg/L) × consumo_total (L) ÷ 1000
- **CO₂ biogênico** (t) = fator_co2_bio (kg/L) × consumo_bio (L) ÷ 1000

A emissão total em **tCO₂e** é obtida aplicando os potenciais de aquecimento global (GWP) do IPCC AR5 (CH₄ = 28, N₂O = 265).

### **Fatores de Emissão**
- Os fatores são obtidos da tabela `lista_emissao_geral` para o veículo específico, ano da frota, país "Brasil" e versão GHG da empresa.
- Se o ano da frota for ≤ 2000, utiliza-se a coluna "2000".
- Os fatores estão em **kg de emissão por litro de combustível** (ou por m³ para GNV).

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
| `404` | Recurso não encontrado | Verifique IDs de veículo, empresa, unidade |
| `500` | Erro interno do servidor | Consulte os logs do sistema |

---

## **Exemplos de Erro Comuns**

### **Veículo não encontrado:**
```json
{
  "error": "Veículo ID 99 não encontrado para a versão GHG v2025.0.1"
}
```

### **Fatores não encontrados:**
```json
{
  "error": "Fatores de emissão não encontrados para veículo 'Carro Flex' ano 2020"
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
3. **Tabela específica:** Os parâmetros de entrada são persistidos na tabela `cat_casa_trabalho_1`, com campos `dias_trabalhos_mes`, `dias_trabalhados_ano`, `consumo_medio_dia`, `is_ida_volta`.
4. **Categoria:** A categoria de Casa Trabalho 1 é obtida dinamicamente. Se não existir, o sistema retorna erro. Cadastre-a previamente na tabela `categorias_emissao`.
5. **Auditoria:** Todos os cálculos persistentes passam por validação de auditoria (`@require_audit_validation`), garantindo rastreabilidade.
6. **Hierarquia flexível:** A consulta de hierarquia permite definir o nível inicial e inclui recursivamente todas as unidades descendentes.
7. **Veículos disponíveis:** Consulte o endpoint `/veiculos` para obter a lista completa de veículos cadastrados.