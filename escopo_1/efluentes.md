# **Efluentes**

Permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes do **tratamento e disposição de efluentes líquidos** (esgotos, efluentes industriais, etc.), com suporte a **um ou dois estágios de tratamento** e parâmetros como carga orgânica, nitrogênio, metano recuperado e fatores de emissão específicos por versão GHG.

---

## **Endpoints de Cálculo**

### **1. POST /api/efluentes/calcular**

Calcula emissões de GEE para um sistema de tratamento de efluentes **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "id_efluente": 2,
  "tratamento1_id": 5,
  "disposicao_final_id": 3,
  "cnpj": "00.000.000/0001-91",
  "quantidade_gerada_anual": 15000.0,
  "organico_entrada1": 1200.5,
  "unidade1": "kg_DBO",
  "dois_tratamentos": false
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_efluente` | número | ID do tipo de efluente (consulte catálogo) | 2 |
| `tratamento1_id` | número | ID do primeiro tratamento | 5 |
| `disposicao_final_id` | número | ID da disposição final do efluente tratado | 3 |
| `cnpj` | string | CNPJ da empresa para obtenção da versão GHG | "00.000.000/0001-91" |

#### **Parâmetros Opcionais (dependem do sistema/modelo):**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `quantidade_gerada_anual` | número | Volume anual de efluente gerado (m³) | 15000.0 |
| `organico_entrada1` | número | Carga orgânica na entrada do tratamento 1 | 1200.5 |
| `organico_lodo1` | número | Carga orgânica removida como lodo (trat. 1) | 300.0 |
| `unidade1` | string | Unidade da carga orgânica (kg_DBO, kg_DQO, etc.) | "kg_DBO" |
| `nitrogenio_entrada1` | número | Nitrogênio total na entrada do tratamento 1 | 50.0 |
| `fator_n2o_tratamento1` | número | Fator de emissão de N₂O para o tratamento 1 (kg N₂O/kg N) | 0.005 |
| `ch4_recuperado1` | número | Metano recuperado no tratamento 1 (m³) | 100.0 |
| `quantidade_apos_tratamento1` | número | Volume de efluente após tratamento 1 (m³) | 12000.0 |
| `dois_tratamentos` | boolean | Indica se há segundo estágio de tratamento | false |
| `tratamento2_id` | número | ID do segundo tratamento | 7 |
| `organico_entrada2` | número | Carga orgânica na entrada do tratamento 2 | 800.0 |
| `organico_lodo2` | número | Carga orgânica removida como lodo (trat. 2) | 200.0 |
| `unidade2` | string | Unidade da carga orgânica (trat. 2) | "kg_DBO" |
| `nitrogenio_apos_tratamento1` | número | Nitrogênio remanescente após tratamento 1 | 30.0 |
| `fator_n2o_tratamento2` | número | Fator de emissão de N₂O para o tratamento 2 | 0.003 |
| `ch4_recuperado2` | número | Metano recuperado no tratamento 2 (m³) | 50.0 |
| `quantidade_lancada` | número | Volume de efluente lançado no corpo receptor | 10000.0 |
| `organico_final` | número | Carga orgânica no efluente final | 500.0 |
| `unidade_final` | string | Unidade da carga orgânica final | "kg_DBO" |
| `nitrogenio_final` | número | Nitrogênio no efluente final | 20.0 |
| `fator_n2o_disposicao_final` | número | Fator de emissão de N₂O para disposição final | 0.01 |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/efluentes/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_efluente": 2,
    "tratamento1_id": 5,
    "disposicao_final_id": 3,
    "cnpj": "00.000.000/0001-91",
    "quantidade_gerada_anual": 15000.0,
    "organico_entrada1": 1200.5,
    "unidade1": "kg_DBO",
    "dois_tratamentos": false
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões realizado com sucesso",
  "data": {
    "emissao_ch4": 1250.3,
    "emissao_n2o_direta": 15.2,
    "emissao_n2o_indireta": 8.1,
    "emissao_total_co2e": 3450.7,
    "metadados": {
      "tratamento": {
        "id": 5,
        "tratamento": "Lagoa anaeróbia"
      }
    }
  }
}
```

*Nota:* Os campos retornados dependem da implementação do `calculator`. A estrutura acima é ilustrativa.

---

### **2. POST /api/efluentes**

Calcula e persiste emissões de GEE para um sistema de efluentes **com persistência no banco** (cria documento, registra parâmetros de entrada e emissões).

#### **Requisição:**
```json
{
  "id_efluente": 2,
  "tratamento1_id": 5,
  "disposicao_final_id": 3,
  "cnpj": "00.000.000/0001-91",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "nvl_unidade_negocio": 2,
  "data": "2024-07-20",
  "quantidade_gerada_anual": 15000.0,
  "organico_entrada1": 1200.5,
  "unidade1": "kg_DBO",
  "dois_tratamentos": false,
  "descricao": "ETE Industrial - Mês de referência",
  "cnpj_fornecedor": "12.345.678/0001-90",
  "link_referencia": "http://exemplo.com/laudos/ete.pdf",
  "id_usuario": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_efluente` | número | ID do tipo de efluente | 2 |
| `tratamento1_id` | número | ID do primeiro tratamento | 5 |
| `disposicao_final_id` | número | ID da disposição final | 3 |
| `id_empresa` | string | UUID da empresa | "dd68b58c-9155..." |
| `id_unidade_negocio` | string | UUID da unidade de negócio | "680a4fe4-d2f1..." |
| `nvl_unidade_negocio` | número | Nível hierárquico (1–10) | 2 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-20" |
| `cnpj` | string | CNPJ da empresa (para versão GHG) | "00.000.000/0001-91" |

#### **Parâmetros Opcionais (modelo de cálculo):**
Os mesmos campos opcionais listados no endpoint `/calcular`, todos utilizados no cálculo e persistidos na tabela de emissões.

#### **Parâmetros Opcionais (documento):**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_usuario` | string | UUID do usuário responsável |
| `descricao` | string | Descrição adicional |
| `cnpj_fornecedor` | string | CNPJ do fornecedor (ex.: operadora da ETE) |
| `link_referencia` | string | Link para documento comprobatório |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/efluentes" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_efluente": 2,
    "tratamento1_id": 5,
    "disposicao_final_id": 3,
    "cnpj": "00.000.000/0001-91",
    "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
    "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
    "nvl_unidade_negocio": 2,
    "data": "2024-07-20",
    "quantidade_gerada_anual": 15000.0,
    "organico_entrada1": 1200.5,
    "unidade1": "kg_DBO"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões de efluentes realizado com sucesso",
  "data": {
    "resultado_calculo": {
      "emissao_ch4": 1250.3,
      "emissao_n2o_direta": 15.2,
      "emissao_n2o_indireta": 8.1,
      "emissao_total_co2e": 3450.7
    },
    "ids_gerados": {
      "documento_id": "550e8400-e29b-41d4-a716-446655440000",
      "emissao_id": 456
    },
    "metadados": {
      "empresa": "Empresa Exemplo",
      "unidade": "Filial São Paulo",
      "data": "2024-07-20"
    }
  }
}
```

#### **Notas Importantes:**
- Endpoint submete à **validação de auditoria** (`@require_audit_validation`).
- A categoria do documento é obtida dinamicamente via `db.get_categoria_efluentes_id()`.
- A empresa e a unidade devem existir e a unidade deve pertencer à empresa.
- O CNPJ é utilizado para obter a versão GHG, que determina os fatores de emissão padrão para tratamentos e disposição final.
- **Não há tabela específica para efluentes** (como `combustao_movel_1_id`); os parâmetros de entrada são salvos diretamente na tabela `emissoes` (colunas específicas).

---

## **Endpoints de Consulta**

### **1. GET /api/efluentes/unidade**

Consulta emissões de efluentes agregadas por unidade de negócio, com possibilidade de incluir unidades descendentes.

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
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/efluentes/unidade?id_unidade=680a4fe4-d2f1-4f2d-ab99-556effb557c9&nivel=2&incluir_descendentes=true" \
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
    "detalhes": [
      {
        "documento_id": "550e8400-e29b-41d4-a716-446655440000",
        "data": "2024-07-20",
        "descricao": "ETE Industrial - Mês de referência",
        "unidade": {
          "id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
          "nome": "Filial São Paulo",
          "nivel": 2
        },
        "emissoes": {
          "ch4": 1250.3,
          "n2o_direta": 15.2,
          "n2o_indireta": 8.1,
          "total_co2e": 3450.7
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

### **2. GET /api/efluentes/{documento_id}/detalhes**

Consulta detalhes completos de um documento específico de efluentes.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path parameter) |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/efluentes/550e8400-e29b-41d4-a716-446655440000/detalhes" \
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
      "descricao": "ETE Industrial - Mês de referência",
      "cnpj_fornecedor": "12.345.678/0001-90",
      "link_referencia": "http://exemplo.com/laudos/ete.pdf",
      "id_categoria": 4,
      "id_usuario": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-20T14:30:00Z"
    },
    "emissoes": {
      "id": 456,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "id_efluente": 2,
      "id_tratamento_1": 5,
      "id_tratamento_2": null,
      "id_disposicao_final": 3,
      "quantidade_gerada_anual": 15000.0,
      "organico_entrada_1": 1200.5,
      "organico_lodo_1": null,
      "unidade_1": "kg_DBO",
      "nitrogenio_entrada_1": null,
      "fator_n2o_tratamento_1": null,
      "ch4_recuperado_1": null,
      "quantidade_apos_tratamento_1": null,
      "organico_entrada_2": null,
      "organico_lodo_2": null,
      "unidade_2": null,
      "nitrogenio_apos_tratamento_1": null,
      "fator_n2o_tratamento_2": null,
      "ch4_recuperado_2": null,
      "quantidade_lancada": null,
      "organico_final": null,
      "unidade_final": null,
      "nitrogenio_final": null,
      "fator_n2o_disposicao_final": null,
      "dois_tratamentos": false,
      "created_at": "2024-07-20T14:30:00Z"
    }
  }
}
```

---

### **3. GET /api/efluentes/hierarquia-completa**

Retorna a hierarquia completa de emissões de efluentes para uma empresa, agregando emissões por unidade de negócio e suas descendentes.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_empresa` | ✓ | string | UUID da empresa | — |
| `nivel_inicio` | ✗ | número | Nível hierárquico inicial (1–10) | 1 |
| `ano` | ✗ | string | Ano para filtrar (formato YYYY) | — |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/efluentes/hierarquia-completa?id_empresa=dd68b58c-9155-42df-97ba-8ec86953c360&nivel_inicio=1&ano=2024" \
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
| `/efluentes/calcular` | POST | `calcular:escopo1` |
| `/efluentes` (persistência) | POST | `calcular:escopo1` |
| `/efluentes/unidade` | GET | `consultar:resultados` |
| `/efluentes/{documento_id}/detalhes` | GET | `consultar:resultados` |
| `/efluentes/hierarquia-completa` | GET | `consultar:resultados` |

**Header de Autenticação:**
```http
X-API-Key: sua-chave-api-aqui
```

---

## **Regras de Cálculo Atualizadas**

### **Versão GHG**
- A versão GHG é obtida automaticamente a partir do **CNPJ** informado (endpoint `/calcular`) ou do CNPJ da empresa (endpoint com persistência).
- Os fatores de emissão para tratamentos e disposição final são específicos da versão GHG e são buscados dinamicamente no banco.

### **Fluxo de Cálculo:**
1. **Obter versão GHG** através do CNPJ.
2. **Validar IDs** de efluente, tratamento(s) e disposição final; verificar se existem para a versão GHG informada.
3. **Calcular emissões** com base nos parâmetros fornecidos:
   - Emissões de CH₄ provenientes da degradação anaeróbia da matéria orgânica.
   - Emissões diretas de N₂O durante o tratamento.
   - Emissões indiretas de N₂O decorrentes da deposição de nitrogênio em corpos hídricos.
   - Emissões evitadas pela recuperação de metano (CH₄ recuperado).
4. **Aplicar fatores de emissão** padrão (quando não informados pelo usuário) ou utilizar os fatores fornecidos na requisição.
5. **Consolidar emissões** em CO₂ equivalente (GWP 100 anos).

### **Validações:**
- **CNPJ obrigatório** em `/calcular`; no fluxo persistente, o CNPJ é obtido da empresa.
- **Empresa e unidade** devem existir e estar ativas.
- **Nível da unidade** deve estar entre 1 e 10.
- **Categoria de Efluentes** deve estar configurada no sistema.
- **Unidade de negócio** deve pertencer à empresa informada.
- **Auditoria automática** é aplicada no fluxo persistente.

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Campos obrigatórios ausentes ou inválidos | Verifique o corpo da requisição |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Verifique suas permissões de usuário |
| `404` | Recurso não encontrado | Verifique IDs de empresa, unidade, efluente, tratamento, disposição final |
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

### **Tratamento não encontrado:**
```json
{
  "error": "Tratamento ID 99 não encontrado"
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

### **Unidade não pertence à empresa:**
```json
{
  "error": "Unidade de negócio não pertence à empresa"
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
2. **Versão GHG dinâmica:** Os fatores de emissão para tratamentos e disposição final são vinculados à versão GHG da empresa.
3. **Flexibilidade de parâmetros:** O modelo de cálculo aceita uma ampla gama de parâmetros; nem todos são obrigatórios. Consulte a documentação do `calculator` para entender as dependências.
4. **Sistema com um ou dois tratamentos:** Utilize o campo `dois_tratamentos` (booleano) e, quando verdadeiro, preencha os campos relativos ao segundo tratamento (`tratamento2_id`, `organico_entrada2`, etc.).
5. **Persistência direta na tabela `emissoes`:** Diferentemente de outros módulos, os parâmetros de entrada dos efluentes são salvos diretamente na tabela de emissões, em colunas dedicadas. Não há tabela intermediária `combustao_*`.
6. **Categoria:** A categoria de efluentes é obtida dinamicamente; certifique-se de que esteja cadastrada no sistema.
7. **Hierarquia flexível:** A consulta de hierarquia permite definir o nível inicial e inclui recursivamente todas as unidades descendentes.
8. **Auditoria:** Todos os cálculos persistentes passam por validação de auditoria, garantindo rastreabilidade e conformidade.

