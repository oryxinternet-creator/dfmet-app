# Fluxos n8n — DFNET

Dois fluxos prontos para importar no n8n da DFNET (menu **Import from File**):

1. **`dfnet-webhooks-app.json`** — os webhooks que o app chama
   (`app-login`, `app-boletos`, `app-os`, `app-desbloqueio`, `app-contrato-aceite`,
   `app-promocoes`, `app-token`).
2. **`dfnet-notificacoes.json`** — notificações push
   (aviso manual por formulário, "fatura vence amanhã" e "detecção de bloqueio").

Já estão com o **SGP da DFNET** (`https://dfnet.sgp.net.br`). Falta só preencher os
valores marcados como `TROCAR_...`:

| Placeholder | O que é | Onde conseguir |
|---|---|---|
| `TROCAR_TOKEN_SGP_DFNET` | Token da API do SGP da DFNET | Painel do SGP/TSMX da DFNET |
| `TROCAR_PROJETO_FIREBASE_DFNET` | ID do projeto Firebase (usado na URL do FCM) | Console do Firebase do app DFNET |
| `TROCAR_ID_PLANILHA_DFNET` | ID da planilha Google (abas `tokens` e `promocoes`) | URL da planilha da DFNET |
| `TROCAR_CRED_GOOGLE_DFNET` | Credencial Google (Service Account) no n8n | Criar/selecionar ao importar |
| `TROCAR_CRED_FCM_DFNET` | Credencial Google p/ enviar FCM no n8n | Criar/selecionar ao importar |

## Passos

1. Importe os dois arquivos no n8n.
2. Em cada nó **Google Sheets** e **HTTP Request (FCM)**, selecione/cadastre as credenciais
   Google da DFNET (o n8n vai pedir).
3. Substitua os `TROCAR_...` (token SGP, ID da planilha, ID do projeto Firebase).
4. A planilha Google precisa ter as abas **`tokens`** (colunas: cpf, contrato, token,
   plataforma, data, ultimo_status) e **`promocoes`** (colunas: ativo, ordem, titulo, texto,
   cor, imagem, link, botao).
5. Ative os dois fluxos. Copie a **URL base dos webhooks** (ex.: `https://SEU-N8N/webhook`)
   e cole na constante `API_BASE` em `src/App.jsx` do app.

> Observação: os caminhos da API do SGP (`/api/ura/...`, `/api/os/list/` etc.) são os mesmos
> do padrão SGP/TSMX. Se o SGP da DFNET usar caminhos diferentes, ajuste nos nós HTTP.
