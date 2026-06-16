# App DFNET Telecomunicações — Pacote para Publicação

Olá! Este é o projeto do aplicativo **DFNET Telecomunicações** (Android), pronto para publicar
na Google Play Store. Abaixo, o que é cada coisa e por onde começar.

## Comece por aqui

1. **`GUIA-PLAYSTORE.md`** — o passo a passo completo: como gerar o `.aab` assinado
   (pelo GitHub Actions ou local), configurar a keystore e preencher a Play Console.
   **Leia este primeiro.**

2. **`POLITICA-DE-PRIVACIDADE.md`** — texto pronto da política de privacidade
   (a Play Store exige uma URL pública com isso).

## O que tem no pacote

| Item | Descrição |
|---|---|
| `src/`, `package.json`, `vite.config.js`, `index.html`, `capacitor.config.json` | Código do app (React + Vite + Capacitor) |
| `google-services.json` | Configuração do Firebase (push) — já incluída |
| `app-icons/` | Ícones do app (logo DFNET) em todas as densidades + adaptive icon |
| `.github/workflows/build-apk.yml` | Build de APK **debug** (para testes) |
| `.github/workflows/build-release.yml` | Build de **AAB assinado** (para a Play Store) |
| `play-store-assets/` | Ícone 512, banner 1024×500 e screenshots para a ficha da loja |
| `GUIA-PLAYSTORE.md` | Guia de publicação |
| `POLITICA-DE-PRIVACIDADE.md` | Política de privacidade |

## Resumo técnico

- **Package / App ID:** `com.dfnet.cliente`
- **Nome:** DFNET Telecomunicações
- **Versão:** 1.0.0
- **Stack:** React + Vite + Capacitor 6, Firebase Cloud Messaging
- **Backend:** webhooks n8n da DFNET ⚠️ (ajustar API_BASE — ver CONFIGURAR-DFNET.md)
- **Build:** via GitHub Actions (a pasta `android/` é gerada automaticamente)

## Caminho mais rápido para publicar

1. Gerar uma keystore (`keytool`, ver guia)
2. Cadastrar os 4 secrets no GitHub (`KEYSTORE_BASE64`, `KEYSTORE_PASSWORD`, `KEY_ALIAS`, `KEY_PASSWORD`)
3. Actions → **Build Release (Play Store)** → Run workflow
4. Baixar o artefato **`dfnet-app-aab-playstore`** (`app-release.aab`)
5. Subir esse `.aab` na Play Console + preencher a ficha (textos e imagens já prontos no pacote)

Qualquer dúvida sobre o app, webhooks ou Firebase, falar com o Daniel (DFNET Telecomunicações).
