# Configuração

Este guia cobre o setup local da documentação e os pontos principais para quem precisa consumir ou manter a API.

## Variáveis de ambiente

As integrações do backend normalmente dependem de credenciais e parâmetros como:

- `SUPABASE_URL` ou a URL do serviço de dados usado pela API
- `SUPABASE_KEY` ou a chave de acesso correspondente
- `API_BASE_URL` para clientes que consomem a API localmente
- `X-API-Key` no cliente para autenticação de requisições

Quando houver um arquivo `.env` no backend, use os valores oficiais do ambiente de desenvolvimento da equipe.

## Setup local

1. Clone o repositório.
2. Abra a pasta no VS Code.
3. Inicie um servidor local para a documentação, se necessário.
4. Valide os links do menu lateral e navegue pelas páginas principais.

Se você estiver mantendo a API em paralelo, siga as instruções do backend para instalar dependências e subir o serviço antes de testar os exemplos.

## Docker

O Docker é o caminho preferencial para reproduzir o ambiente de forma consistente.

### Subir o ambiente

```bash
docker compose up --build
```

### Encerrar o ambiente

```bash
docker compose down
```

### Boas práticas

- Use volumes quando precisar persistir dados entre reinícios.
- Mantenha as credenciais fora do repositório.
- Confirme a porta exposta antes de apontar o frontend ou os testes automatizados.
