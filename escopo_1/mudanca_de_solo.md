# **Mudança no Uso do Solo**

Permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes de **mudanças no uso do solo** (conversão de vegetação nativa para outros usos, alterações entre categorias de uso, etc.), conforme metodologias do GHG Protocol para o setor de AFOLU. O cálculo considera **estado**, **bioma**, **tipos de uso do solo (anterior e posterior)**, **área convertida** e, opcionalmente, **estoques primários de carbono** ou **fatores de emissão personalizados**.

---

## **Endpoints de Cálculo**

### **1. POST /api/mudanca-uso-solo/calcular**

Calcula emissões de GEE para uma conversão de uso do solo **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "id_estado": 1,
  "id_uso_anterior_solo": 6,
  "id_uso_posterior_solo": 6,
  "id_bioma_anterior": 1,
  "area_mus": 12.0,
  "tipo_vegetacao_anterior": "Primaria",
  "fitofisionomia_anterior": false,
  "id_bioma_posterior": 3,
  "dados_primarios_estoque_carbono": true,
  "estoque_carbono_solo": 25.0,
  "estoque_carbono_biomassa": 80.0,
  "detalhamento_vegetacao_anterior": "Vegetacao natural, nao especificada",
  "fator_personalizado": null,
  "cnpj": "00.000.000/0001-91"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_estado` | número | ID do estado brasileiro (consulte catálogo) | 1 |
| `id_uso_anterior_solo` | número | ID do uso do solo anterior à conversão | 6 |
| `id_uso_posterior_solo` | número | ID do uso do solo posterior à conversão | 6 |
| `area_mus` | número | Área da mudança de uso do solo (ha) | 12.0 |
| `cnpj` | string | CNPJ da empresa para obtenção da versão GHG | "00.000.000/0001-91" |

#### **Parâmetros Condicionalmente Obrigatórios:**
| Campo | Tipo | Condição | Descrição |
|-------|------|----------|-----------|
| `id_bioma_anterior` | número | Se `id_uso_anterior_solo` for **Vegetação Natural** | ID do bioma da área convertida |
| `id_bioma_posterior` | número | Se `id_uso_posterior_solo` for **Vegetação Natural** | ID do bioma da área após conversão |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `tipo_vegetacao_anterior` | string | "Primaria" ou "Secundaria" (vegetação natural) |
| `fitofisionomia_anterior` | boolean | Fitofisionomia da vegetação anterior |
| `fitofisionomia_posterior` | boolean | Fitofisionomia da vegetação posterior |
| `dados_primarios_estoque_carbono` | boolean | Indica se serão informados estoques primários |
| `estoque_carbono_solo` | número | Estoque de carbono no solo (tC/ha) – dados primários |
| `estoque_carbono_biomassa` | número | Estoque de carbono na biomassa (tC/ha) – dados primários |
| `detalhamento_vegetacao_anterior` | string | Descrição adicional da vegetação anterior |
| `detalhamento_vegetacao_posterior` | string | Descrição adicional da vegetação posterior |
| `fator_personalizado` | número | Fator de emissão personalizado (tCO₂e/ha) |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/mudanca-uso-solo/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_estado": 1,
    "id_uso_anterior_solo": 6,
    "id_uso_posterior_solo": 6,
    "id_bioma_anterior": 1,
    "area_mus": 12.0,
    "cnpj": "00.000.000/0001-91"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões realizado com sucesso",
  "data": {
    "emissao_total_co2": 125.3,
    "emissao_total_ch4": 0,
    "emissao_total_n2o": 0,
    "emissao_total_biogenico": 125.3,
    "metadados": {
      "estado": {
        "id": 1,
        "sigla": "AC",
        "nome": "Acre"
      },
      "uso_solo": {
        "id_uso_anterior_solo": 6,
        "uso_anterior_nome": "Vegetação Natural",
        "id_uso_posterior_solo": 6,
        "uso_posterior_nome": "Vegetação Natural"
      },
      "bioma_anterior": {
        "id": 1,
        "bioma": "Amazônia"
      }
    }
  }
}
```

#### **Notas sobre o Cálculo:**
- A versão GHG é obtida automaticamente a partir do **CNPJ** informado.
- Os estoques de carbono e fatores de emissão são obtidos das tabelas de referência do sistema, a menos que sejam fornecidos dados primários ou fator personalizado.
- Emissões de CO₂ (não‑biogênico) e emissões biogênicas são diferenciadas conforme a origem do carbono (fóssil vs. biomassa).

---

### **2. POST /api/mudanca-uso-solo**

Calcula e persiste emissões de GEE para uma conversão de uso do solo **com persistência no banco** (cria documento, registro de mudança de uso do solo e emissões).

#### **Requisição:**
```json
{
  "id_estado": 1,
  "id_uso_anterior_solo": 6,
  "id_uso_posterior_solo": 6,
  "id_bioma_anterior": 1,
  "area_mus": 12.0,
  "tipo_vegetacao_anterior": "Primaria",
  "fitofisionomia_anterior": false,
  "id_bioma_posterior": 3,
  "dados_primarios_estoque_carbono": true,
  "estoque_carbono_solo": 25.0,
  "estoque_carbono_biomassa": 80.0,
  "detalhamento_vegetacao_anterior": "Vegetacao natural, nao especificada",
  "fator_personalizado": null,
  "cnpj": "00.000.000/0001-91",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "nvl_unidade_negocio": 2,
  "data": "2024-07-20",
  "descricao": "Conversão de vegetação nativa para pastagem",
  "cnpj_fornecedor": "12.345.678/0001-90",
  "link_referencia": "http://exemplo.com/laudo",
  "id_usuario": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_estado` | número | ID do estado | 1 |
| `id_uso_anterior_solo` | número | ID do uso anterior | 6 |
| `id_uso_posterior_solo` | número | ID do uso posterior | 6 |
| `area_mus` | número | Área convertida (ha) | 12.0 |
| `id_empresa` | string | UUID da empresa | "dd68b58c-9155..." |
| `id_unidade_negocio` | string | UUID da unidade de negócio | "680a4fe4-d2f1..." |
| `nvl_unidade_negocio` | número | Nível hierárquico (1–10) | 2 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-20" |
| `cnpj` | string | CNPJ da empresa (versão GHG) | "00.000.000/0001-91" |

#### **Parâmetros Condicionalmente Obrigatórios (cálculo):**
Os mesmos do endpoint `/calcular` (`id_bioma_anterior` se uso anterior for vegetação natural, etc.).

#### **Parâmetros Opcionais (cálculo):**
Os mesmos do endpoint `/calcular` (dados primários, fatores personalizados, etc.).

#### **Parâmetros Opcionais (documento):**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_usuario` | string | UUID do usuário responsável |
| `descricao` | string | Descrição adicional |
| `cnpj_fornecedor` | string | CNPJ do fornecedor |
| `link_referencia` | string | Link para documento comprobatório |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/mudanca-uso-solo" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_estado": 1,
    "id_uso_anterior_solo": 6,
    "id_uso_posterior_solo": 6,
    "id_bioma_anterior": 1,
    "area_mus": 12.0,
    "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
    "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
    "nvl_unidade_negocio": 2,
    "data": "2024-07-20",
    "cnpj": "00.000.000/0001-91"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões de mudança no uso do solo realizado com sucesso",
  "data": {
    "resultado_calculo": {
      "emissao_total_co2": 125.3,
      "emissao_total_ch4": 0,
      "emissao_total_n2o": 0,
      "emissao_total_biogenico": 125.3
    },
    "ids_gerados": {
      "documento_id": "550e8400-e29b-41d4-a716-446655440000",
      "mudanca_uso_solo_id": 123,
      "emissao_id": 456
    },
    "metadados": {
      "estado": {
        "id": 1,
        "sigla": "AC",
        "nome": "Acre"
      },
      "uso_solo": {
        "id_uso_anterior_solo": 6,
        "uso_anterior_nome": "Vegetação Natural",
        "id_uso_posterior_solo": 6,
        "uso_posterior_nome": "Vegetação Natural"
      }
    }
  }
}
```

#### **Notas Importantes:**
- Endpoint submete à **validação de auditoria** (`@require_audit_validation`).
- A categoria do documento é obtida dinamicamente via `db.get_categoria_mudanca_id()` (fallback recomendado: **5** ou conforme cadastro).
- A empresa e a unidade devem existir e estar ativas.
- O CNPJ é utilizado para obter a versão GHG, que influencia os fatores de emissão de estoques de carbono.
- Os parâmetros de entrada são persistidos na tabela **`cat_mudanca_uso_solo`**.
- As emissões são persistidas na tabela `emissoes`, com distinção entre CO₂ fóssil e biogênico.

---

## **Endpoints de Consulta**

### **1. GET /api/mudanca-uso-solo/unidade**

Consulta emissões de mudança no uso do solo agregadas por unidade de negócio, com possibilidade de incluir unidades descendentes.

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
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/mudanca-uso-solo/unidade?id_unidade=680a4fe4-d2f1-4f2d-ab99-556effb557c9&nivel=2&incluir_descendentes=true" \
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
    "emissao_total": 125.3,
    "quantidade_documentos": 1,
    "quantidade_unidades": 1,
    "detalhes": [
      {
        "documento_id": "550e8400-e29b-41d4-a716-446655440000",
        "data": "2024-07-20",
        "descricao": "Conversão de vegetação nativa para pastagem",
        "unidade": {
          "id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
          "nome": "Filial São Paulo",
          "nivel": 2
        },
        "emissoes": {
          "co2": 125.3,
          "ch4": 0,
          "n2o": 0,
          "total": 125.3,
          "biogenica": 125.3
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

### **2. GET /api/mudanca-uso-solo/{documento_id}/detalhes**

Consulta detalhes completos de um documento específico de mudança no uso do solo.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path parameter) |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/mudanca-uso-solo/550e8400-e29b-41d4-a716-446655440000/detalhes" \
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
      "descricao": "Conversão de vegetação nativa para pastagem",
      "cnpj_fornecedor": "12.345.678/0001-90",
      "link_referencia": "http://exemplo.com/laudo",
      "id_categoria": 5,
      "id_usuario": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-20T14:30:00Z"
    },
    "mudanca_uso_solo": {
      "id": 123,
      "id_estado": 1,
      "id_uso_anterior_solo": 6,
      "id_uso_posterior_solo": 6,
      "id_bioma_anterior": 1,
      "id_bioma_posterior": 3,
      "area_mus": 12.0,
      "tipo_vegetacao_anterior": "Primaria",
      "fitofisionomia_anterior": false,
      "fitofisionomia_posterior": null,
      "dados_primarios_estoque_carbono": true,
      "estoque_carbono_solo": 25.0,
      "estoque_carbono_biomassa": 80.0,
      "detalhamento_vegetacao_anterior": "Vegetacao natural, nao especificada",
      "detalhamento_vegetacao_posterior": null,
      "fator_personalizado": null,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-20T14:30:00Z"
    },
    "emissoes": {
      "id": 456,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "emissao_co2": 125.3,
      "emissao_ch4": 0,
      "emissao_n2o": 0,
      "emissao_total": 125.3,
      "emissao_biogenica": 125.3,
      "created_at": "2024-07-20T14:30:00Z"
    }
  }
}
```

---

### **3. GET /api/mudanca-uso-solo/hierarquia-completa**

Retorna a hierarquia completa de emissões de mudança no uso do solo para uma empresa, agregando emissões por unidade de negócio e suas descendentes.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_empresa` | ✓ | string | UUID da empresa | — |
| `nivel_inicio` | ✗ | número | Nível hierárquico inicial (1–10) | 1 |
| `ano` | ✗ | string | Ano para filtrar (formato YYYY) | — |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/mudanca-uso-solo/hierarquia-completa?id_empresa=dd68b58c-9155-42df-97ba-8ec86953c360&nivel_inicio=1&ano=2024" \
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
    "emissao_total_empresa": 125.3,
    "nivel_inicio": 1,
    "quantidade_unidades_raiz": 1,
    "periodo": {
      "ano": "2024",
      "filtro_aplicado": true
    },
    "hierarquia": [
      {
        "id": "unidade-1-uuid",
        "nivel": 1,
        "nome": "Matriz",
        "emissao_direta": 0,
        "emissao_total": 125.3,
        "quantidade_documentos": 0,
        "filhas": [
          {
            "id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
            "nivel": 2,
            "nome": "Filial São Paulo",
            "emissao_direta": 125.3,
            "emissao_total": 125.3,
            "quantidade_documentos": 1,
            "filhas": []
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
| `/mudanca-uso-solo/calcular` | POST | `calcular:escopo1` |
| `/mudanca-uso-solo` (persistência) | POST | `calcular:escopo1` |
| `/mudanca-uso-solo/unidade` | GET | `consultar:resultados` |
| `/mudanca-uso-solo/{documento_id}/detalhes` | GET | `consultar:resultados` |
| `/mudanca-uso-solo/hierarquia-completa` | GET | `consultar:resultados` |

**Header de Autenticação:**
```http
X-API-Key: sua-chave-api-aqui
```

---

## **Regras de Cálculo Atualizadas**

### **Metodologia**
O cálculo das emissões/remoções de carbono por mudança de uso do solo segue o método **Gain‑Loss** do IPCC, com adaptações para os biomas brasileiros e dados do Sistema de Estimativas de Emissões e Remoções de Gases de Efeito Estufa (SEEG).

1. **Obtenção da versão GHG** a partir do CNPJ informado (cálculo) ou do CNPJ da empresa (persistência).
2. **Identificação dos usos do solo** anterior e posterior, buscando nas tabelas de referência os **estoques de carbono** (biomassa e solo) para cada uso, estado e bioma.
3. **Cálculo da diferença de estoque**:
   ```
   ΔC = (C_anterior - C_posterior) × área
   ```
   Onde `C` é o estoque de carbono por hectare (tC/ha).
4. **Conversão para CO₂ equivalente**:
   ```
   Emissão CO₂ (t) = ΔC × (44/12)
   ```
5. **Quando o uso posterior é vegetação natural**, considera‑se remoção (ΔC negativo).
6. **Dados primários**: Se `dados_primarios_estoque_carbono = true`, os valores de `estoque_carbono_solo` e `estoque_carbono_biomassa` substituem os fatores padrão.
7. **Fator personalizado**: Se `fator_personalizado` for informado, este substitui todo o cálculo de estoque (emissão = área × fator).

### **Versão GHG**
- Os fatores de estoque de carbono (biomassa e solo) são armazenados nas tabelas de referência e podem variar conforme a **versão GHG** (ex.: IPCC 2006, IPCC 2019 refinements).
- A versão GHG é obtida dinamicamente a partir do **CNPJ** (endpoint `/calcular`) ou do CNPJ da empresa (endpoint com persistência).
- Caso a versão GHG não seja encontrada, o cálculo **não prossegue** – é retornado erro.

### **Validações:**
- **CNPJ obrigatório** em `/calcular`; no fluxo persistente, o CNPJ é obtido da empresa.
- **Empresa e unidade** devem existir e estar ativas.
- **Nível da unidade** deve estar entre 1 e 10.
- **Área** deve ser maior que zero.
- **Bioma** é obrigatório quando o uso do solo é **Vegetação Natural**.
- **Categoria de Mudança no Uso do Solo** deve estar configurada no sistema.
- **Auditoria automática** é aplicada no fluxo persistente.

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Campos obrigatórios ausentes ou inválidos | Verifique o corpo da requisição |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Verifique suas permissões de usuário |
| `404` | Recurso não encontrado | Verifique IDs de empresa, unidade, estado, usos do solo, bioma |
| `500` | Erro interno do servidor | Consulte os logs do sistema |

---

## **Exemplos de Erro Comuns**

### **CNPJ não informado:**
```json
{
  "error": "CNPJ é obrigatório para o cálculo"
}
```

### **Versão GHG não encontrada:**
```json
{
  "error": "Versão GHG não encontrada para o CNPJ: 00.000.000/0001-91"
}
```

### **Estado não encontrado:**
```json
{
  "error": "Estado com id 99 não encontrado"
}
```

### **Uso do solo não encontrado:**
```json
{
  "error": "Uso do solo anterior com id 99 não encontrado"
}
```

### **Bioma obrigatório para vegetação natural:**
```json
{
  "error": "Campo obrigatório 'id_bioma_anterior' não encontrado no corpo da requisição"
}
```

### **Área inválida:**
```json
{
  "error": "Área da mudança de uso do solo deve ser maior que zero"
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

---

## **Observações Importantes**

1. **Dois modos de cálculo:** Utilize `/calcular` para testes e simulações; utilize o POST sem `/calcular` para lançamento oficial com persistência e auditoria.
2. **Versão GHG dinâmica:** Os estoques de carbono de referência são vinculados à versão GHG da empresa. Certifique-se de que o CNPJ esteja cadastrado e possua uma versão GHG ativa.
3. **Usos do solo e biomas:** Consulte as tabelas de apoio (`list_usos_solo`, `list_biomas`) para obter os IDs válidos.
4. **Dados primários vs. fatores padrão:** Quando `dados_primarios_estoque_carbono = true`, os campos `estoque_carbono_solo` e `estoque_carbono_biomassa` **devem** ser preenchidos. Caso contrário, o sistema buscará automaticamente os valores da base de dados.
5. **Fator personalizado:** O campo `fator_personalizado` (tCO₂e/ha) substitui todo o cálculo de estoque. Utilize apenas quando houver um fator de emissão específico para a conversão.
6. **Categoria:** A categoria de Mudança no Uso do Solo é obtida dinamicamente via `db.get_categoria_mudanca_id()` (ou `db.get_categoria_mudanca_uso_solo_id()`). Verifique se a categoria está cadastrada.
7. **Tabela específica:** Os parâmetros de entrada são persistidos na tabela **`cat_mudanca_uso_solo`**, que contém colunas como `id_estado`, `id_uso_anterior_solo`, `id_uso_posterior_solo`, `id_bioma_anterior`, `id_bioma_posterior`, `area_mus`, `tipo_vegetacao_anterior`, etc.
8. **Emissões biogênicas:** As emissões de CO₂ provenientes da biomassa são registradas no campo `emissao_biogenica` da tabela `emissoes`. Emissões de combustíveis fósseis (quando aplicável) vão para `emissao_co2`.
9. **Hierarquia flexível:** A consulta de hierarquia permite definir o nível inicial e inclui recursivamente todas as unidades descendentes.
10. **Auditoria:** Todos os cálculos persistentes passam por validação de auditoria, garantindo rastreabilidade e conformidade.

---

