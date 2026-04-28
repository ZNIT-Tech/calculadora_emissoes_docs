# FAQ

Respostas rápidas para dúvidas comuns sobre a API.

## 1. Qual é o formato padrão de resposta?
As rotas da API usam um envelope com `success`, `data`, `error` e `meta`.

## 2. Qual header de autenticação devo enviar?
Use `X-API-Key`.

## 3. `Authorization: Bearer` também funciona?
Sim, quando o backend aceitar a mesma chave via bearer token.

## 4. Posso enviar qualquer `Content-Type`?
Não. Para payload JSON, use `application/json`.

## 5. O que acontece se eu mandar JSON inválido?
Hoje há um caso conhecido em que a resposta pode voltar como HTML `400 Bad Request`. Isso está documentado como exceção.

## 6. Como interpretar os códigos de erro?
Use `error.code` para mapear a mensagem exibida ao usuário.

## 7. Quando devo tentar retry?
Retry é apropriado para `429` e `503`, com backoff e limite de tentativas.

## 8. As unidades são sempre as mesmas?
Não. Consulte cada endpoint para saber a unidade esperada do campo de entrada e a unidade de saída do cálculo.

## 9. Existe limite de requisições?
Sim. Em caso de excesso, a API pode responder com `429 rate_limited`.

## 10. Onde vejo o significado de cada endpoint?
Na referência da seção correspondente de Escopo 1, 2 ou 3 e nos guias de autenticação e erros.
