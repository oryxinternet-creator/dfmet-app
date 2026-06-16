# Guia de Publicação na Google Play Store — App DFNET Telecomunicações

Este guia é para o **desenvolvedor** que vai publicar o app na Play Store.
O projeto é um app **Capacitor (React + Vite)** empacotado como Android, com build
via **GitHub Actions**. Você pode usar o pipeline que já existe ou gerar localmente.

---

## 1. Visão geral do projeto

- **App ID (package):** `com.dfnet.cliente`
- **Nome do app:** DFNET Telecomunicações
- **Versão:** 1.0.0 (versionCode 1)
- **Stack:** React + Vite (front), Capacitor 6 (wrapper Android), Firebase Cloud Messaging (push)
- **Backend:** o app consome webhooks n8n que por sua vez falam com o SGP da DFNET
  (`https://dfnet.sgp.net.br`). ⚠️ **PENDENTE DFNET:** ajuste a constante `API_BASE` em
  `src/App.jsx` para a base de webhooks do n8n da DFNET (veja `CONFIGURAR-DFNET.md`).
- **Firebase:** o `google-services.json` na raiz é um **PLACEHOLDER**. ⚠️ **PENDENTE DFNET:**
  crie o projeto Firebase da DFNET e substitua esse arquivo pelo real (veja `CONFIGURAR-DFNET.md`).

### Estrutura
```
dfnet-app/
├── src/App.jsx               # todo o app (componente único)
├── src/main.jsx
├── index.html
├── package.json
├── vite.config.js
├── capacitor.config.json     # appId / appName
├── google-services.json      # Firebase (push) — fica na RAIZ
├── app-icons/                # ícones já prontos (logo DFNET) + XMLs adaptive
└── .github/workflows/
    ├── build-apk.yml         # gera APK DEBUG (testes) — já em uso
    └── build-release.yml     # gera AAB ASSINADO (Play Store) — usar este p/ publicar
```

> A pasta `android/` **não** está versionada — o Capacitor a regenera a cada build
> (`npx cap add android`). Por isso o ícone e o `google-services.json` são reaplicados
> pelos passos do workflow. Se for buildar local, rode os mesmos passos.

---

## 2. Caminho recomendado: build de RELEASE pelo GitHub Actions

O workflow `build-release.yml` já gera o **AAB assinado** pronto para a Play Store.
Ele só precisa da **keystore** (chave de assinatura) configurada como secrets.

### 2.1. Gerar a keystore (uma única vez)
Em qualquer máquina com JDK instalado:
```bash
keytool -genkey -v \
  -keystore dfnet-release.jks \
  -alias dfnet \
  -keyalg RSA -keysize 2048 -validity 10000
```
Anote a **senha** e o **alias** (`dfnet`). **Guarde a `dfnet-release.jks` com segurança** —
se perder, não dá para atualizar o app na Play depois (a menos que use Play App Signing,
veja abaixo).

### 2.2. Converter a keystore em base64
```bash
# Linux/Mac
base64 -w0 dfnet-release.jks > keystore_base64.txt
# Mac (alternativa)
base64 -i dfnet-release.jks -o keystore_base64.txt
```

### 2.3. Cadastrar os secrets no GitHub
No repositório: **Settings → Secrets and variables → Actions → New repository secret**.
Crie os 4:

| Secret | Valor |
|---|---|
| `KEYSTORE_BASE64` | conteúdo do `keystore_base64.txt` |
| `KEYSTORE_PASSWORD` | senha da keystore |
| `KEY_ALIAS` | `dfnet` (ou o alias que usou) |
| `KEY_PASSWORD` | senha da chave (normalmente = senha da keystore) |

### 2.4. Rodar o build
Aba **Actions → "Build Release (Play Store)" → Run workflow**.
Ao terminar (~5–7 min), baixe os artefatos:
- **`dfnet-app-aab-playstore`** → `app-release.aab` ← **é este que sobe na Play Store**
- `dfnet-app-apk-release` → APK assinado, útil para testar a versão de produção no aparelho

> **Play App Signing (recomendado):** ao criar o app na Play Console, aceite o
> "Play App Signing". Assim o Google guarda a chave de assinatura final e a sua keystore
> passa a ser só a "upload key". Se um dia perder a upload key, dá para resetar.

---

## 3. Caminho alternativo: build LOCAL

Se preferir gerar na sua máquina (Node 20 + JDK 17 + Android SDK):
```bash
npm install
npm run build
npx cap add android
cp google-services.json android/app/google-services.json
# aplicar ícones (mesmos passos do workflow):
for d in mdpi hdpi xhdpi xxhdpi xxxhdpi; do
  mkdir -p android/app/src/main/res/mipmap-$d
  cp app-icons/ic_launcher_$d.png            android/app/src/main/res/mipmap-$d/ic_launcher.png
  cp app-icons/ic_launcher_round_$d.png      android/app/src/main/res/mipmap-$d/ic_launcher_round.png
  cp app-icons/ic_launcher_foreground_$d.png android/app/src/main/res/mipmap-$d/ic_launcher_foreground.png
done
mkdir -p android/app/src/main/res/values android/app/src/main/res/mipmap-anydpi-v26
cp app-icons/ic_launcher_background.xml android/app/src/main/res/values/ic_launcher_background.xml
cp app-icons/ic_launcher.xml android/app/src/main/res/mipmap-anydpi-v26/ic_launcher.xml
cp app-icons/ic_launcher.xml android/app/src/main/res/mipmap-anydpi-v26/ic_launcher_round.xml
# permissão de push (Android 13+): adicionar no AndroidManifest.xml
#   <uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
npx cap sync android
cd android
./gradlew bundleRelease \
  -Pandroid.injected.signing.store.file=/caminho/dfnet-release.jks \
  -Pandroid.injected.signing.store.password=SENHA \
  -Pandroid.injected.signing.key.alias=dfnet \
  -Pandroid.injected.signing.key.password=SENHA
# saída: android/app/build/outputs/bundle/release/app-release.aab
```

---

## 4. Checklist da Play Console (o que a Google pede)

Para publicar, na **Google Play Console** você vai precisar de:

### Conta e app
- [ ] Conta de desenvolvedor Google Play (US$ 25, pagamento único) — **o amigo já tem**
- [ ] Criar um novo app (nome: *DFNET Telecomunicações*; tipo: App; gratuito)
- [ ] Aceitar o **Play App Signing**

### Ficha da loja (Store listing)
- [ ] **Nome do app:** DFNET Telecomunicações
- [ ] **Descrição curta** (até 80 caracteres) — sugestão no item 5 abaixo
- [ ] **Descrição completa** — sugestão no item 5
- [ ] **Ícone 512×512** → use `app-icons/ic_launcher_512_playstore.png` (incluído)
- [ ] **Gráfico de destaque 1024×500** (banner) — precisa criar (posso ajudar)
- [ ] **Screenshots do app** (mín. 2, recomendado 4–8) — dá para usar as imagens das telas
- [ ] **Categoria:** Ferramentas ou Negócios

### Conteúdo e conformidade
- [ ] **Política de Privacidade** (URL pública obrigatória) — texto pronto incluído
      (`POLITICA-DE-PRIVACIDADE.md`); precisa hospedar numa URL (ex: site da DFNET)
- [ ] **Questionário de classificação de conteúdo** (provavelmente "Livre")
- [ ] **Público-alvo:** maiores de idade / uso geral
- [ ] **Seção "Segurança de dados"**: declarar que o app coleta CPF/login para autenticação,
      usa para funcionamento do serviço, e trafega de forma criptografada. (NÃO vende dados.)
- [ ] **Permissões sensíveis:** o app usa `POST_NOTIFICATIONS` (notificações) e `INTERNET`.
      Não usa localização, câmera, contatos, etc.

### Versão
- [ ] Subir o **`app-release.aab`** na faixa desejada (Teste interno → Produção)
- [ ] Recomendado: começar em **Teste interno**, validar no aparelho, depois promover a Produção.

---

## 5. Textos sugeridos para a ficha da loja

**Nome:** DFNET Telecomunicações

**Descrição curta (80):**
`Sua internet na palma da mão: boletos, suporte e velocidade em um só app.`

**Descrição completa:**
```
O aplicativo oficial da DFNET Telecomunicações coloca tudo o que você precisa na palma da mão.

Com o app você pode:
• Ver e pagar seus boletos — copie o código de barras com um toque
• Consultar o histórico de faturas
• Desbloquear sua conexão pelo desbloqueio de confiança
• Testar a velocidade da sua internet
• Ver e assinar seu contrato
• Acessar o Portal do Assinante
• Falar com o suporte por WhatsApp, telefone ou redes sociais

Receba avisos de vencimento e fique sempre em dia, sem complicação.

Baixe agora e tenha a DFNET Telecomunicações sempre com você.
```

> Para um app **white-label** de outro provedor, basta trocar nome, textos e ícone.

---

## 6. Observações importantes

- **versionCode:** a cada nova versão enviada à Play, incremente o `versionCode`
  (em `android/app/build.gradle`, gerado pelo Capacitor — ou configure no projeto).
  A v1 pode subir como está.
- **applicationId:** `com.dfnet.cliente` — uma vez publicado, **não pode mudar**.
- **google-services.json:** ⚠️ é um **PLACEHOLDER**. Crie o projeto Firebase da DFNET, registre
  o app `com.dfnet.cliente` e troque o arquivo. Sem isso o push não funciona (mas o app builda).
- **Backend:** ⚠️ ajuste `API_BASE` para o n8n da DFNET. Enquanto os webhooks não responderem,
  o app cai em "modo demonstração", então ele nunca quebra na avaliação da Google.

---

## 7. Dúvidas frequentes

**Preciso de servidor?** O app fala com os webhooks n8n da DFNET. Ajuste a base em `API_BASE`
(`src/App.jsx`) — veja `CONFIGURAR-DFNET.md`.

**Preciso configurar o Firebase?** Sim, para a DFNET: crie o projeto Firebase, registre o app
`com.dfnet.cliente` e substitua o `google-services.json` (placeholder). Veja `CONFIGURAR-DFNET.md`.

**Posso testar antes de publicar?** Sim — use o APK de debug (`build-apk.yml`) ou o APK
de release (`dfnet-app-apk-