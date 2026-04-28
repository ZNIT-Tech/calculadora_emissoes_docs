# **Energia por Escolha de Compra**

Endpoint para calcular emissões de energia elétrica a partir da fonte de geração escolhida e dos fatores de emissão associados ao combustível ou informados no payload.

---

## **Endpoint**

### **POST /api/energia-escolha-compra**

#### **Objetivo**
Calcular emissões de Escopo 2 considerando a fonte de geração, a eficiência aplicada e os fatores de emissão de CO2, CH4, N2O e CO2 biogênico.

---

## **Requisição**

#### **Body (JSON)**
```json
{
  "data": "2024-06-15",
  "consumo_mensal": 1000.0,
  "consumo_anual": 0.0,
  "id_fonte_geracao_energia": 1,
  "id_combustivel_termoeletrico": 0,
  "fator_co2": 0.15,
  "fator_ch4": 0.0005,
  "fator_n2o": 0.0002,
  "fator_co2_bio": 0.10,
  "eficiencia_recuperacao": 1.0,
  "fator_personalizado": true
}
```

#### **Parâmetros Obrigatórios**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `data` | string | Data de referência no formato `YYYY-MM-DD` | `2024-06-15` |
| `consumo_mensal` | número | Consumo mensal em MWh | `1000.0` |
| `consumo_anual` | número | Consumo anual em MWh | `5000.0` |
| `id_fonte_geracao_energia` | número | Identificador da fonte de geração. `6` indica termoelétrica | `1` |
| `id_combustivel_termoeletrico` | número | Identificador do combustível termoelétrico | `0` |
| `fator_co2` | número | Fator de CO2 em tCO2/MWh | `0.15` |
| `fator_ch4` | número | Fator de CH4 em tCH4/MWh | `0.0005` |
| `fator_n2o` | número | Fator de N2O em tN2O/MWh | `0.0002` |
| `fator_co2_bio` | número | Fator de CO2 biogênico em tCO2/MWh | `0.10` |
| `eficiencia_recuperacao` | número | Eficiência aplicada ao cálculo, entre 0 e 1 | `1.0` |
| `fator_personalizado` | boolean | Define se os fatores vêm do payload ou do cadastro termoelétrico | `true` |

---

## **Metodologia de Cálculo**

O fluxo segue a lógica abaixo:

1. Define-se o consumo utilizado.
   - Se `consumo_anual > 0`, ele tem prioridade.
   - Caso contrário, usa-se `consumo_mensal`.
2. Verifica-se a fonte de geração.
   - Se `id_fonte_geracao_energia == 6`, o caso é tratado como termoelétrico.
   - Se não for termoelétrica, os fatores do payload são usados diretamente e a eficiência é fixada em `1`.
3. Para termoelétricas:
   - Se `fator_personalizado == true`, os fatores do payload são aplicados.
   - Se `fator_personalizado == false`, o sistema consulta os fatores do combustível termoelétrico no Supabase.
4. A eficiência é normalizada para `1` caso venha nula ou zero.
5. As emissões são calculadas com as fórmulas:

$$
\text{emissao\_co2} = \frac{\text{consumo\_utilizado}}{\text{eficiencia\_utilizada}} \times \text{fator\_co2\_util}
$$

$$
\text{emissao\_ch4} = \frac{\text{consumo\_utilizado}}{\text{eficiencia\_utilizada}} \times \text{fator\_ch4\_util}
$$

$$
\text{emissao\_n2o} = \frac{\text{consumo\_utilizado}}{\text{eficiencia\_utilizada}} \times \text{fator\_n2o\_util}
$$

$$
\text{emissao\_biogenica} = \frac{\text{consumo\_utilizado}}{\text{eficiencia\_utilizada}} \times \text{fator\_co2\_bio\_util}
$$

6. O total em CO2e é obtido com os potenciais de aquecimento global:

$$
\text{emissao\_total} = \text{emissao\_co2} + (\text{emissao\_ch4} \times 28) + (\text{emissao\_n2o} \times 265)
$$

> A emissão biogênica é retornada separadamente e não entra no total de CO2e.

---

## **Exemplo de Requisição**

```bash
curl -X POST http://localhost:8080/api/energia-escolha-compra \
  -H "Content-Type: application/json" \
  -d '{
    "data": "2024-06-15",
    "consumo_mensal": 1000.0,
    "consumo_anual": 0.0,
    "id_fonte_geracao_energia": 1,
    "id_combustivel_termoeletrico": 0,
    "fator_co2": 0.15,
    "fator_ch4": 0.0005,
    "fator_n2o": 0.0002,
    "fator_co2_bio": 0.10,
    "eficiencia_recuperacao": 1.0,
    "fator_personalizado": true
  }'
```

## **Resposta de Sucesso**

```json
{
  "status": "success",
  "data": {
    "fator_co2_utilizado": 0.15,
    "fator_ch4_utilizado": 0.0005,
    "fator_n2o_utilizado": 0.0002,
    "fator_co2_bio_utilizado": 0.10,
    "emissao_co2": 150.0,
    "emissao_ch4": 0.5,
    "emissao_n2o": 0.2,
    "emissao_total": 217.0,
    "emissao_biogenica": 100.0
  }
}
```

### **Exemplo Termoelétrico com Fatores do Cadastro**

Quando `id_fonte_geracao_energia == 6` e `fator_personalizado == false`, os fatores são buscados no Supabase pela tabela de combustíveis termoelétricos. Nesse caso, a resposta também devolve os fatores efetivamente usados.

---

## **Erros e Tratamento**

- Campos obrigatórios ausentes retornam HTTP 400.
- Data inválida retorna HTTP 400 com mensagem específica.
- Combustível termoelétrico ausente no cadastro retorna HTTP 500.
- Erros não previstos retornam HTTP 500.

---

## **Referências de Código**

- `src/app/escopos/escopo_2/energia_escolha_compra/blueprint.py`
- `src/app/escopos/escopo_2/energia_escolha_compra/calculator.py`
