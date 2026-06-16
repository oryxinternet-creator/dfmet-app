# ⚙️ Configurar a versão DFNET — o que falta preencher

A marca já está 100% trocada (logo, cores, nome, slogan, contatos, textos, ícones).
Para o app **funcionar de verdade** (login, boletos, push), faltam alguns dados que só
você tem. Abaixo, tudo o que precisa ser preenchido e onde.

## ✅ Já feito
- Logo, ícones do app (todas as densidades) e ícone 512 da loja
- Cores DFNET (amarelo `#FFCC00`, laranja `#EA7A0C`, preto)
- Nome **DFNET** / **DFNET Telecomunicações** e slogan "Seja fibra, navegue sem limites!"
- App ID **`com.dfnet.cliente`**
- WhatsApp **(61) 99123-1566** e link da central/2ª via **dfnet.sgp.net.br**
- SGP da DFNET (`https://dfnet.sgp.net.br`) já nos fluxos n8n
- Banner 1024×500 da loja

## 🔧 Pendente (preencher)

### 1. Base dos webhooks n8n  → `src/App.jsx`
Linha da constante `API_BASE`. Hoje está com um valor placeholder. Coloque a URL base dos
webhooks do **n8n da DFNET** (ex.: `https://SEU-N8N/webhook`).

### 2. Firebase (push)  → `google-services.json` (raiz)
O arquivo atual é um **dummy válido** — serve só para o app **compilar e ser testado**. As
notificações push **só funcionam** depois que você criar o projeto Firebase da DFNET, registrar
o app Android `com.dfnet.cliente` e substituir o `google-services.json` pelo real.

### 3. Fluxos n8n  → pasta `n8n/`
Importe os dois fluxos e troque os `TROCAR_...`:
- `TROCAR_TOKEN_SGP_DFNET` — token da API do SGP da DFNET
- `TROCAR_PROJETO_FIREBASE_DFNET` — ID do projeto Firebase (mesmo do item 2)
- `TROCAR_ID_PLANILHA_DFNET` — planilha Google com abas `tokens` e `promocoes`
- credenciais Google do n8n (o n8n pede ao importar)
Detalhes em `n8n/LEIA-N8N.md`.

### 4. Detalhes opcionais  → `src/App.jsx`
- **Instagram / Facebook:** hoje apontam para `dfnet.com.br` (não achei os perfis). Se a DFNET
  tiver redes sociais, me passe os @ que eu troco.
- **Portal do Assinante** (aba "Meus Apps"): aponta para `dfnet.com.br`. Se houver um portal de
  streaming/benefícios, me passe a URL.
- **Horário de atendimento** (tela Suporte): está com um horário genérico — confirme o real.
- **Endereço:** usei a área de cobertura (Nova Colina / Arapoanga). Se tiver endereço de loja, me diga.

### 5. Keystore de assinatura  → GitHub Secrets
Para gerar o `.aab` da Play Store (ver `GUIA-PLAYSTORE.md`): gere a keystore e cadastre os
4 secrets (`KEYSTORE_BASE64`, `KEYSTORE_PASSWORD`, `KEY_ALIAS`, `KEY_PASSWORD`).

### 6. Screenshots da loja  → `play-store-assets/screenshots/`
Capturar do app DFNET já rodando (as antigas eram de outra marca). Ver
`play-store-assets/screenshots/LEIA-SCREENSHOTS.md`.

---

**Resumo:** me mande **(1)** a URL base do n8n da DFNET, **(2)** o `google-services.json` do
Firebase da DFNET, e **(3)** o token do SGP da DFNET — com isso o app fica 100% funcional.
