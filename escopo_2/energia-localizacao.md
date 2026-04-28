# **Energia por Localização**

Endpoint para calcular emissões de energia elétrica com base no fator de emissão da rede elétrica do SIN para a data informada.

> Observação: no backend atual, o blueprint da rota está registrado com prefixo `/api/v1`; para a documentação do frontend, a referência de consumo é mantida como `/api/energia-localizacao`.

---

## **Endpoint**

### **POST /api/energia-localizacao**

#### **Objetivo**
Calcular emissões de Escopo 2 a partir do consumo mensal ou anual de energia elétrica e do fator de emissão associado ao mês/ano de referência.

---

## **Requisição**

#### **Body (JSON)**
```json
{
  "categoria": "Energia Elétrica (Localização)",
  "consumo_mensal": 1000.0,
  "consumo_anual": 0.0,
  "data": "2024-01-15",
  "cnpj_usuario": "12345678901234",
  "pais": "Brasil"
}
```

#### **Parâmetros Obrigatórios**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `categoria` | string | Categoria do lançamento, usada como metadado | `Energia Elétrica (Localização)` |
| `consumo_mensal` | número | Consumo mensal em MWh. Se maior que zero, tem prioridade sobre o anual | `1000.0` |
| `consumo_anual` | número | Consumo anual em MWh | `12000.0` |
| `data` | string | Data de referência no formato `YYYY-MM-DD` | `2024-01-15` |
| `cnpj_usuario` | string | CNPJ do usuário/empresa para regras especiais e rastreabilidade | `12345678901234` |

#### **Parâmetros Opcionais**
| Campo | Tipo | Descrição | Padrão |
|-------|------|-----------|--------|
| `pais` | string | País usado na consulta do fator de emissão | `Brasil` |

---

## **Metodologia de Cálculo**

O cálculo é feito em três etapas:

1. O backend define o consumo utilizado.
   - Se `consumo_mensal > 0`, usa `consumo_mensal`.
   - Caso contrário, usa `consumo_anual`.
2. O fator de emissão é determinado pela data de referência.
   - Para a regra geral, o sistema consulta o fator do mês correspondente ou o valor anual (`Yearly`) no Supabase.
3. A emissão final é calculada por multiplicação direta:

$$
\text{total\_emissao} = \text{consumo\_usado} \times \text{fe\_sin}
$$

### **Caso especial Mitre 2024**

Se o `cnpj_usuario` for `07882930000165` ou `11111111111111` e o ano da `data` for `2024`, o cálculo usa uma tabela fixa de fatores mensais do SIN. Nesse caso:

- Se `consumo_mensal > 0`, o mês da data define o fator.
- Se `consumo_mensal == 0`, o fator é zero.
- O retorno contém `total_emissao`, `fe_sin`, `consumo_mensal` e `consumo_anual`.

---

## **Exemplo de Requisição**

```bash
curl -X POST http://localhost:8080/api/energia-localizacao \
  -H "Content-Type: application/json" \
  -d '{
    "categoria": "Energia Elétrica (Localização)",
    "consumo_mensal": 1000.0,
    "consumo_anual": 0.0,
    "data": "2024-01-15",
    "cnpj_usuario": "12345678901234",
    "pais": "Brasil"
  }'
```

## **Resposta de Sucesso**

```json
{
  "status": "success",
  "data": {
    "total_emissao": 42.1001,
    "fe_sin": 0.0421001,
    "consumo_mensal": 1000.0,
    "consumo_anual": 0.0,
    "pais": "Brasil"
  }
}
```

### **Exemplo do Caso Especial Mitre 2024**

```json
{
  "status": "success",
  "data": {
    "total_emissao": 56.364,
    "fe_sin": 0.112728,
    "consumo_mensal": 500.0,
    "consumo_anual": 0.0
  }
}
```

---

## **Erros e Tratamento**

- Campos obrigatórios ausentes retornam HTTP 400 com mensagem indicando quais campos faltaram.
- Data inválida retorna HTTP 400.
- Erros não previstos retornam HTTP 500.

---

## **Referências de Código**

- `src/app/escopos/escopo_2/energia_localizacao/blueprint.py`
- `src/app/escopos/escopo_2/energia_localizacao/calculator.py`
