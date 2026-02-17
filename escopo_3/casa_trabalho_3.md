# **Casa Trabalho 3 (Escopo 3)**

Este módulo permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes de **deslocamentos casa-trabalho** baseados na **distância percorrida** (categoria 3). Aqui, utiliza-se o veículo, a distância média diária e os dias trabalhados para estimar o consumo de combustível, considerando o consumo médio do veículo (km/L ou km/kWh) e os percentuais mensais de mistura (etanol na gasolina, biodiesel no diesel).

---

## **Endpoints de Cálculo**

### **1. POST /api/casa-trabalho-3/calcular**

Calcula emissões de GEE para um deslocamento casa-trabalho **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "id_veiculo": 5,
  "ano_frota": 2020,
  "distancia_media_dia": 50,
  "distancia_mensal": 22,
  "distancia_anual": 0,
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
| `distancia_media_dia` | número | Distância média percorrida por dia (km/dia) | 50 |
| `data` | string | Data de referência (YYYY-MM-DD) para percentuais de mistura | "2024-01-15" |
| `ida_volta` | boolean | Se `true`, considera ida e volta (dobra as emissões) | true |
| `cnpj` | string | CNPJ da empresa (para obter a versão GHG) | "12.345.678/0001-90" |

**Obrigatório (pelo menos um):**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `distancia_mensal` | número | Dias trabalhados no mês (ou quilometragem mensal) |
| `distancia_anual` | número | Dias trabalhados no ano (ou quilometragem anual) |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_empresa` | string | UUID da empresa (metadados) |
| `id_unidade_negocio` | string | UUID da unidade de negócio (metadados) |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-3/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q" \
  -d '{
    "id_veiculo": 5,
    "ano_frota": 2020,
    "distancia_mensal": 22,
    "distancia_anual": 0,
    "distancia_media_dia": 50,
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
    "consumo_fossil": 88.33,
    "consumo_biocombustivel": 32.67,
    "fator_co2": 2.2,
    "fator_co2_biogenico": 1.5,
    "fator_ch4": 0.1,
    "fator_n2o": 0.02,
    "emissao_co2": 0.19433,
    "emissao_co2_biogenico": 0.04901,
    "emissao_ch4": 0.0121,
    "emissao_n2o": 0.00242,
    "emissao_total": 1.042,
    "unidade": "km/L",
    "versao_ghg": "v2025.0.1",
    "metadados": {
      "veiculo": {
        "id": 5,
        "nome": "Automóvel flex",
        "fossil": "Gasolina Automotiva (pura)",
        "biocombustivel": "Etanol Hidratado"
      },
      "ano_frota": 2020,
      "distancia_media_dia": 50,
      "ida_volta": true,
      "data_referencia": "2024-01-15"
    }
  }
}
```

---

### **2. POST /api/casa-trabalho-3**

Calcula e persiste emissões de GEE para um deslocamento casa-trabalho **com persistência no banco** (cria documento, registro na tabela específica e emissões).

#### **Requisição:**
```json
{
  "id_veiculo": 5,
  "ano_frota": 2020,
  "distancia_media_dia": 50,
  "distancia_mensal": 22,
  "distancia_anual": 0,
  "data": "2024-01-15",
  "ida_volta": true,
  "id_empresa": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
  "id_unidade_negocio": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a",
  "nvl_unidade_negocio": 2,
  "descricao": "Deslocamento diário - veículo flex",
  "cnpj_fornecedor": "12.345.678/0001-90",
  "link_referencia": "http://exemplo.com/planilha",
  "id_usuario": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_veiculo` | número | ID do veículo |
| `ano_frota` | número | Ano de fabricação |
| `distancia_media_dia` | número | Distância média diária (km/dia) |
| `data` | string | Data de referência |
| `ida_volta` | boolean | Ida e volta? |
| `id_empresa` | string | UUID da empresa |
| `id_unidade_negocio` | string | UUID da unidade de negócio |
| `nvl_unidade_negocio` | número | Nível hierárquico (1–10) |

**Obrigatório (pelo menos um):**
- `distancia_mensal` ou `distancia_anual`

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_usuario` | string | UUID do usuário responsável |
| `descricao` | string | Descrição adicional |
| `cnpj_fornecedor` | string | CNPJ do fornecedor (se aplicável) |
| `link_referencia` | string | Link para documento comprobatório |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-3" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q" \
  -d '{
    "id_veiculo": 5,
    "ano_frota": 2020,
    "distancia_media_dia": 50,
    "distancia_mensal": 22,
    "distancia_anual": 0,
    "data": "2024-01-15",
    "ida_volta": true,
    "id_empresa": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
    "id_unidade_negocio": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a",
    "nvl_unidade_negocio": 2,
    "descricao": "Deslocamento diário - veículo flex"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo e persistência realizados",
  "data": {
    "resultado_calculo": {
      "consumo_fossil": 88.33,
      "consumo_biocombustivel": 32.67,
      "emissao_total": 1.042,
      "emissao_co2_biogenico": 0.04901,
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
      "veiculo": "Automóvel flex",
      "data": "2024-01-15"
    }
  }
}
```

---

## **Endpoints de Consulta**

### **1. GET /api/casa-trabalho-3/unidade**

Consulta emissões de casa trabalho 3 agregadas por unidade de negócio, com possibilidade de incluir unidades descendentes.

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
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-3/unidade?id_unidade=1b3e142c-a8f2-4fe4-a0eb-3b138967f03a&nivel=2&incluir_descendentes=true&data_inicio=2024-01-01&data_fim=2024-12-31" \
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
    "quantidade_documentos": 3,
    "quantidade_unidades": 2,
    "detalhes": [
      {
        "documento_id": "550e8400-e29b-41d4-a716-446655440000",
        "data": "2024-01-15",
        "descricao": "Casa Trabalho 3 - Veículo: Automóvel flex",
        "unidade": {
          "id": "1b3e142c-a8f2-4fe4-a0eb-3b138967f03a",
          "nome": "Filial São Paulo",
          "nivel": 2
        },
        "emissoes": {
          "co2": 0.19433,
          "ch4": 0.0121,
          "n2o": 0.00242,
          "total": 1.042,
          "biogenica": 0.04901
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

### **2. GET /api/casa-trabalho-3/{documento_id}/detalhes**

Consulta detalhes completos de um documento específico de casa trabalho 3.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path) |

#### **Exemplo de Uso:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-3/550e8400-e29b-41d4-a716-446655440000/detalhes" \
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
      "descricao": "Casa Trabalho 3 - Veículo: Automóvel flex",
      "link_referencia": null,
      "id_categoria": 10,
      "id_usuario": null,
      "created_at": "2024-01-15T10:30:00Z"
    },
    "entrada": {
      "id": 123,
      "id_veiculo": 5,
      "ano_frota": 2020,
      "dias_trabalhados_mes": 22,
      "dias_trabalhados_ano": null,
      "distancia_media_dia": 50,
      "is_ida_volta": true,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-01-15T10:30:00Z",
      "lista_veiculos_combustiveis": {
        "id": 5,
        "veiculo": "Automóvel flex",
        "fossil": "Gasolina Automotiva (pura)",
        "biocombustivel": "Etanol Hidratado",
        "co2_fossil": 2.2,
        "co2_biocombustivel": 1.5,
        "ch4_fossil": 0.1,
        "ch4_biocombustivel": 0.05,
        "n2o_fossil": 0.02,
        "n2o_biocombustivel": 0.01
      }
    },
    "emissoes": {
      "id": 456,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "emissao_co2": 0.19433,
      "emissao_ch4": 0.0121,
      "emissao_n2o": 0.00242,
      "emissao_total": 1.042,
      "emissao_biogenica": 0.04901,
      "created_at": "2024-01-15T10:30:00Z"
    }
  }
}
```

---

### **3. GET /api/casa-trabalho-3/veiculos**

Lista todos os veículos disponíveis para cálculo (tabela `lista_veiculos_combustiveis`).

#### **Exemplo de Uso:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-3/veiculos" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q"
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "veiculo": "Automóvel gasolina",
      "fossil": "Gasolina Automotiva (pura)",
      "biocombustivel": "-",
      "co2_fossil": 2.2,
      "co2_biocombustivel": 0,
      "ch4_fossil": 0.1,
      "ch4_biocombustivel": 0,
      "n2o_fossil": 0.02,
      "n2o_biocombustivel": 0,
      "versao_ghg": "v2025.0.1"
    },
    {
      "id": 2,
      "veiculo": "Caminhão diesel",
      "fossil": "Óleo Diesel (puro)",
      "biocombustivel": "-",
      "co2_fossil": 2.6,
      "co2_biocombustivel": 0,
      "ch4_fossil": 0.15,
      "ch4_biocombustivel": 0,
      "n2o_fossil": 0.03,
      "n2o_biocombustivel": 0,
      "versao_ghg": "v2025.0.1"
    },
    {
      "id": 5,
      "veiculo": "Automóvel flex",
      "fossil": "Gasolina Automotiva (pura)",
      "biocombustivel": "Etanol Hidratado",
      "co2_fossil": 2.2,
      "co2_biocombustivel": 1.5,
      "ch4_fossil": 0.1,
      "ch4_biocombustivel": 0.05,
      "n2o_fossil": 0.02,
      "n2o_biocombustivel": 0.01,
      "versao_ghg": "v2025.0.1"
    }
  ],
  "total": 3
}
```

---

### **4. GET /api/casa-trabalho-3/hierarquia-completa**

Retorna a hierarquia completa de emissões de casa trabalho 3 para uma empresa, agregando por unidade de negócio e suas descendentes.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_empresa` | ✓ | string | UUID da empresa | — |
| `nivel_inicio` | ✗ | número | Nível hierárquico inicial (1–10) | 1 |
| `ano` | ✗ | string | Ano para filtrar (YYYY) | — |

#### **Exemplo de Uso:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/casa-trabalho-3/hierarquia-completa?id_empresa=bf725c29-2b8c-46dc-a3ad-880e511e7804&nivel_inicio=1&ano=2024" \
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
| `/casa-trabalho-3/calcular` | POST | `calcular:escopo3` |
| `/casa-trabalho-3` (persistência) | POST | `calcular:escopo3` |
| `/casa-trabalho-3/unidade` | GET | `consultar:resultados` |
| `/casa-trabalho-3/{documento_id}/detalhes` | GET | `consultar:resultados` |
| `/casa-trabalho-3/veiculos` | GET | `consultar:resultados` |
| `/casa-trabalho-3/hierarquia-completa` | GET | `consultar:resultados` |

**Header de Autenticação:**
```http
X-API-Key: sua-chave-api-aqui
```

---

## **Regras de Cálculo**

### **Metodologia**
O cálculo segue a abordagem de **distância percorrida**:

```
Distância total (km) = (dias mês OU dias ano) × distância média diária (km/dia)
```

Em seguida, converte-se a distância em consumo de combustível utilizando o consumo médio do veículo (obtido da tabela `lista_consumo_medio`):

```
Consumo total (L ou kWh) = distância total (km) / consumo médio (km/L ou km/kWh)
```

Para veículos a combustão, o consumo total é dividido em fração fóssil e biocombustível com base nos percentuais mensais de mistura (etanol na gasolina, biodiesel no diesel), obtidos da tabela `lista_percentual_sin`.

Para veículos elétricos (unidade `km / kWh`), utiliza-se o fator de emissão do sistema elétrico (FE SIN) obtido da mesma tabela de percentuais, com chave `"fe sin"` (em tCO₂/MWh). O resultado é convertido para tCO₂.

As emissões são calculadas utilizando fatores de emissão da tabela `lista_veiculos_combustiveis`:

- **CO₂ fóssil** (t) = co2_fossil (kg/L) × consumo_fóssil (L) ÷ 1000
- **CH₄** (t) = (ch4_fossil × consumo_fóssil + ch4_bio × consumo_bio) ÷ 1000
- **N₂O** (t) = (n2o_fossil × consumo_fóssil + n2o_bio × consumo_bio) ÷ 1000
- **CO₂ biogênico** (t) = co2_bio × consumo_bio ÷ 1000

A emissão total em **tCO₂e** é obtida aplicando os potenciais de aquecimento global (GWP) do IPCC AR5 (CH₄ = 28, N₂O = 265). Para veículos elétricos, a emissão total é apenas o CO₂ calculado.

### **Fatores de Emissão**
- Os fatores são obtidos da tabela `lista_veiculos_combustiveis` para o veículo específico e versão GHG da empresa.
- Os fatores estão em **kg de emissão por litro de combustível** (ou para elétricos, o FE SIN em tCO₂/MWh é aplicado diretamente ao consumo em kWh).

### **Consumo Médio do Veículo**
- O consumo médio (km/L ou km/kWh) é obtido da tabela `lista_consumo_medio` com base no tipo de veículo e ano da frota (coluna do ano, ex: "2020").
- Para veículos elétricos, a unidade é `km / kWh`. O cálculo então usa o FE SIN.

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

### **Consumo médio não encontrado:**
```json
{
  "error": "Consumo médio não encontrado para veículo 'Automóvel flex' ano 2020"
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
3. **Tabela específica:** Os parâmetros de entrada são persistidos na tabela `cat_casa_trabalho_3`, com campos `dias_trabalhados_mes`, `dias_trabalhados_ano`, `distancia_media_dia`, `ano_frota`, `id_veiculo`, `is_ida_volta`.
4. **Categoria:** A categoria de Casa Trabalho 3 é obtida dinamicamente. Se não existir, o sistema retorna erro. Cadastre-a previamente na tabela `categorias_emissao` com `tabela = 'cat_casa_trabalho_3'`.
5. **Auditoria:** Todos os cálculos persistentes passam por validação de auditoria (`@require_audit_validation`), garantindo rastreabilidade.
6. **Hierarquia flexível:** A consulta de hierarquia permite definir o nível inicial e inclui recursivamente todas as unidades descendentes.
7. **Veículos disponíveis:** Consulte o endpoint `/veiculos` para obter a lista completa de veículos cadastrados e seus fatores.
8. **Veículos elétricos:** Para veículos elétricos, o cálculo é simplificado: `emissão = (distância / consumo_medio) * fe_sin / 1000`. O `fe_sin` é obtido da tabela `lista_percentual_sin` para o ano corrente (considerado "Yearly") e país "Brasil".