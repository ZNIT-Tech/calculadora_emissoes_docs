# Testes

Esta página descreve como validar a documentação e como expandir a suíte de verificação.

## Como rodar os testes

1. Suba a API em ambiente local ou no container de desenvolvimento.
2. Execute os testes automatizados com a base correta da API.
3. Confira o status HTTP, o envelope de erro e os exemplos publicados no docs.

Exemplo de execução local:

```bash
python tools/test_errors_v2.py --base-url http://localhost:8080/api/v1 --cases tools/testcases_v2.json
```

## Estrutura dos testes

- Casos de sucesso validam o contrato esperado da resposta.
- Casos de erro validam status, `success`, `error.code` e mensagens sugeridas.
- Casos de conformidade verificam se a resposta segue o mesmo formato em todas as rotas documentadas.

## Como adicionar novos testes

1. Inclua um novo caso no arquivo de casos do runner local.
2. Defina método, rota, payload e status esperado.
3. Se o erro precisar de variação, aceite mais de um status quando o backend ainda estiver em transição.
4. Reexecute a suíte e atualize a documentação se o contrato mudar.

## Critérios de aceitação

- O teste deve falhar se o status vier diferente do documentado.
- O teste deve falhar se o corpo não vier em JSON quando o contrato exigir JSON.
- O teste deve registrar exceções conhecidas separadamente, para não mascarar divergências reais.
