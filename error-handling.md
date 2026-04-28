# Guia de Tratamento de Erros da API

Use este guia para decidir o que exibir ao usuário, quando bloquear a ação e quando retry faz sentido.

## Formato esperado
- Rotas `/api/*` devem responder com envelope JSON: `{"success": false, "error": {...}, "meta": {...}}`.
- Endpoints legados ou administrativos podem fugir desse padrão; os testes devem validar essa exceção.

## Códigos por categoria

| Categoria | Códigos | Ação no frontend | Retry |
| --- | --- | --- | --- |
| Validação (400) | `validation_error`, `missing_field`, `invalid_format` | Mostrar erro no campo e permitir correção | Não |
| Auth (401/403) | `unauthorized`, `invalid_api_key`, `forbidden`, `insufficient_permissions` | Pedir nova chave ou exibir permissão negada | Não |
| Negócio (404/409/422) | `not_found`, `conflict`, `business_rule_violation`, `unprocessable_entity` | Explicar o motivo e orientar a próxima ação | Não |
| Limite (429) | `rate_limited` | Informar excesso de requisições | Sim, com backoff |
| Servidor (500/503) | `internal_error`, `service_unavailable` | Mensagem genérica e registro do erro | Só 503, com backoff |

## Mensagens sugeridas
- 400: "Verifique os dados informados e tente novamente."
- 401: "Acesso não autorizado. Verifique sua chave de API."
- 403: "Você não tem permissão para essa ação."
- 404: "Recurso não encontrado."
- 409/422: "Não foi possível concluir a operação. Revise os dados e tente novamente."
- 429: "Muitas solicitações. Tente novamente em alguns segundos."
- 500: "Erro interno. Tente novamente mais tarde."
- 503: "Serviço temporariamente indisponível. Tente novamente em alguns minutos."

## Exemplos de resposta
```json
{ "success": false, "error": { "code": "invalid_api_key", "message": "API key inválida ou expirada" } }
```

```json
{ "success": false, "error": { "code": "conflict", "message": "Já existe um cadastro com este CNPJ" } }
```

```json
{ "success": false, "error": { "code": "rate_limited", "message": "too many requests" }, "meta": { "retry_after": 10 } }
```

```json
{ "success": false, "error": { "code": "service_unavailable", "message": "supabase timeout" } }
```

## Consistência
- O cliente deve usar `error.code` para mapear mensagens localizadas.
- `error.message` é técnico e não deve ir direto para a UI.
- Em operações idempotentes, retry só faz sentido para `429` e `503`.

### Exceção conhecida
- O teste de JSON malformado ainda retorna HTML `400 Bad Request` em vez de envelope JSON.
- No frontend, trate como falha de validação genérica e registre como não conforme.
- Correção pendente: capturar `BadRequest` no backend e responder com o envelope padrão.

## Checklist de validação
- Provocar cada erro e conferir status, `success:false` e `error.code`.
- Confirmar que respostas `/api/*` não variam de formato, exceto a exceção conhecida acima.
- Verificar se os testes de payload inválido, auth, limite e indisponibilidade batem com o que foi documentado.