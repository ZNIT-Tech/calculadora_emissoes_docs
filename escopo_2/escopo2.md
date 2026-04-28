# **Escopo 2**

O Escopo 2 concentra as emissões indiretas associadas ao consumo de energia elétrica. Nesta documentação, os endpoints cobrem dois modelos de cálculo:

- **Energia por localização**: usa o fator de emissão da rede elétrica do SIN para o mês ou ano de referência.
- **Energia por escolha de compra**: usa fatores vinculados à fonte de geração e, quando aplicável, ao combustível termoelétrico.

---

## **Endpoints**

### **1. POST /api/energia-localizacao**

Calcula emissões com base no fator de emissão da localidade/ano informado.

- Documentação completa: [energia-localizacao.md](energia-localizacao.md)

### **2. POST /api/energia-escolha-compra**

Calcula emissões com base na fonte de geração selecionada e nos fatores informados ou consultados no cadastro de combustíveis termoelétricos.

- Documentação completa: [energia-escolha-compra.md](energia-escolha-compra.md)

---

## **Metodologia Geral do Escopo 2**

O Escopo 2 segue a lógica de consumo multiplicado por fator de emissão, com variações conforme a estratégia de compra/contabilização:

1. Determina-se o consumo usado no cálculo.
2. Seleciona-se o fator de emissão apropriado para a abordagem escolhida.
3. Aplica-se a fórmula de emissão correspondente.
4. O resultado final é retornado em CO2e, com os fatores utilizados no cálculo para rastreabilidade.

---

## **Referências de Código**

- `src/app/escopos/escopo_2/energia_localizacao/blueprint.py`
- `src/app/escopos/escopo_2/energia_localizacao/calculator.py`
- `src/app/escopos/escopo_2/energia_escolha_compra/blueprint.py`
- `src/app/escopos/escopo_2/energia_escolha_compra/calculator.py`
