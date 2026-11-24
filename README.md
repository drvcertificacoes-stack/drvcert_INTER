# Site DRV Certificações: atualização de 24/11/2026

Este pacote corrige o erro de "site não seguro", configura o domínio **https://www.drvcertificacoes.com** e traz os ajustes visuais pedidos.

## Arquivos entregues

| Arquivo                          | Para que serve                                                                 |
| :------------------------------- | :----------------------------------------------------------------------------- |
| `drv-deploy-www-public_html.zip` | Site pronto para publicar. O **conteúdo** vai na raiz do site na hospedagem.   |
| `drv-fonte-alterados.zip`        | Arquivos-fonte alterados, com o caminho de cada um no projeto, para o repositório. |

## O que mudou

### Domínio, SSL e redirecionamento

- O domínio canônico passou a ser `https://www.drvcertificacoes.com`, com `www`. Antes era `drvcertificacoes.com`, sem `www`.
- O domínio novo vale para canonical, hreflang, sitemap, `robots.txt`, Open Graph e JSON-LD.
- O `.htaccess` leva qualquer acesso para `https://www.drvcertificacoes.com`, sempre em um único redirecionamento:
  - de `http://` para HTTPS;
  - da versão sem `www` para a versão com `www`;
  - de `/` para `/pt/`.
- O `.htaccess` agora deixa passar o caminho `/.well-known/` sem redirecionar. Antes ele redirecionava tudo, o que impedia o AutoSSL de validar o domínio e emitir o certificado.
- O HSTS está **desligado** até o certificado estar válido nas duas versões do domínio (veja a seção [HSTS](#hsts)).
- Foram conferidas as 112 URLs do sitemap. Todas usam `www` e existem no build. As páginas de privacidade e de agradecimento ficaram fora do sitemap.

### Conteúdo e visual

- **E-mail:** passou a ser `comercial@drvcertificacoes.com`, em todo o site.
- **Ícones:** `favicon.ico`, `favicon.svg`, `apple-touch-icon.png`, `icon-192.png` e `icon-512.png` agora mostram a logo da DRV. Antes era um "D" azul provisório.
- **Texto justificado:** os parágrafos estão justificados, com separação de sílabas automática para não abrir espaços grandes entre as palavras.
  - Ficam fora da justificação as legendas em maiúsculas, os títulos em formato de parágrafo e os textos centralizados.
- **Botão de contato:** "Solicitar proposta" virou "Falar com especialista". A tradução aparece nos 4 idiomas (Talk to an expert, Hablar con un especialista, Parler à un expert).
- **WhatsApp:** o botão flutuante agora é redondo, verde, com brilho nas laterais. O visual foi recriado em código com o ícone gratuito do Simple Icons.
- **Cards de normas na home:** foram incluídas a ISO 9001, a ISO 14001 e a ISO 45001 no início e a ISO/IEC 42001 no fim. A grade agora tem 16 cards.

## Como publicar (cPanel)

1. **Confira a raiz do documento.** Em cPanel → **Domínios**, veja qual pasta o `drvcertificacoes.com` usa. Pode ser `public_html` ou uma pasta própria do domínio.
2. **Faça backup.** Baixe uma cópia da pasta atual antes de apagar qualquer coisa.
3. **Limpe a pasta.** Apague o conteúdo antigo dela.
4. **Envie e extraia o zip.** Envie o `drv-deploy-www-public_html.zip` para essa pasta e use **Extract**.
   - O `index.html`, o `.htaccess` e as pastas `_astro/`, `pt/`, `en/`, `es/` e `fr/` precisam ficar direto na raiz da pasta, e não dentro de uma subpasta.
5. **Confira o `.htaccess`.** Ele é um arquivo oculto. Ative **Mostrar arquivos ocultos** no Gerenciador de Arquivos para ver se ele foi extraído.
6. **Gere o certificado.** Em **SSL/TLS Status**, clique em **Run AutoSSL** e confirme que o certificado cobre `drvcertificacoes.com` e `www.drvcertificacoes.com`.
7. **Limpe o cache do servidor.** Se houver LiteSpeed Cache ou Cloudflare, limpe o cache depois de publicar.

O DNS das duas versões do domínio, com e sem `www`, precisa apontar para a hospedagem. Sem isso, o AutoSSL não emite o certificado.

## Como conferir se a versão nova está no ar

- **Pelo código-fonte:** abra o site em aba anônima, aperte `Ctrl+U` e procure por `BaseLayout.`.

  | Arquivo de CSS encontrado                                    | Versão no ar |
  | :----------------------------------------------------------- | :----------- |
  | `BaseLayout.wf563f39c.css`                                   | Nova         |
  | `BaseLayout.B1_ws0ZJ.css` ou `BaseLayout.CG57hNpm.css`       | Antiga       |

- **Pela home:** ela deve mostrar 16 cards de normas, começando pela ISO 9001, e o botão "Falar com especialista".
- **Pelo terminal:** rode `curl -I https://drvcertificacoes.com`. A linha `Location:` deve mostrar `https://www.drvcertificacoes.com/`.
- **Pelo cadeado:** clique no cadeado do navegador. O certificado deve ser emitido para `www.drvcertificacoes.com`, e não para `.com.br`.

## Problemas comuns

| Sintoma                                            | Causa provável e solução                                                                                                                                                                                               |
| :------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| "Site não seguro" ou certificado do `.com.br`      | O AutoSSL ainda não incluiu o `.com`. Adicione o domínio em **Domínios** e rode o **Run AutoSSL**. Se falhar, o DNS do `.com` ainda não aponta para a hospedagem.                                                    |
| O site continua indo para `.com.br`                | Há um redirecionamento antigo em cPanel → **Redirecionamentos**, um `.htaccess` antigo, que pode estar inclusive na pasta acima do `public_html`, ou um 301 guardado no navegador. Remova a regra e teste em aba anônima. |
| A versão antiga continua aparecendo                | O zip foi extraído numa pasta que não é a raiz do domínio, ou o deploy pelo **Git Version Control** do cPanel publicou de novo os arquivos antigos da raiz do repositório.                                                |
| O site abre "quebrado", sem estilo                 | Os arquivos estão dentro de uma subpasta, e os caminhos `/_astro/…` não encontram o CSS. Mova o conteúdo para a raiz do domínio.                                                                                         |
| O ícone da aba não mudou                           | É cache do navegador. O favicon pode demorar a atualizar. Teste em aba anônima.                                                                                                                                       |

## Arquivos-fonte alterados

Estes são os arquivos que estão em `drv-fonte-alterados.zip`. Copie cada um para o mesmo caminho no projeto.

- `astro.config.mjs`: domínio com `www`.
- `README.md`: notas de domínio atualizadas.
- `deploy/apache/.htaccess`: libera o `/.well-known/` e adiciona o tipo `.webp`.
- `src/config/site.ts`: e-mail `comercial@drvcertificacoes.com`.
- `src/styles/global.css`: texto justificado com hifenização e estilo `bg-whatsapp-icon`.
- `src/components/layout/WhatsAppButton.astro`: botão redondo.
- `src/components/sections/Certifications.astro`: cards ISO 9001, 14001, 45001 e 42001.
- `src/components/sections/Hero.astro`, `Contact.astro`, `ContactForm.astro`: removido o `text-pretty`, que conflitava com a justificação.
- `src/components/ui/PageHeader.astro`, `SectionHeading.astro`: removido o `text-pretty`, pelo mesmo motivo.
- `src/pages/[lang]/[thankYou].astro`: removido o `text-pretty`, pelo mesmo motivo.
- `src/i18n/locales/pt.ts`, `en.ts`, `es.ts`, `fr.ts`: texto do botão e títulos dos novos cards.
- `public/favicon.ico`, `favicon.svg`, `apple-touch-icon.png`, `icon-192.png`, `icon-512.png`: logo da DRV.

## Como gerar o build de novo

Com os arquivos-fonte atualizados no projeto, rode:

```sh
npm ci
npm run build:apache
```

O conteúdo de `dist/`, já com o `.htaccess` gerado, é o que vai para a hospedagem.

O build entregue neste pacote foi gerado a partir do `dist/` existente, com as mesmas alterações aplicadas manualmente, porque o `node_modules` do projeto foi instalado no Windows. Depois de rodar o build na sua máquina, o CSS terá outro nome de arquivo, mas o resultado visual deve ser o mesmo.

## Recomendações

- **Publique só o `dist/`.** A raiz do repositório tem cópias antigas de build: `index.html`, `_astro/`, `pt/`, `en/`, `es/`, `fr/`, os `drv-logo-*` e o `sitemap-*.xml`. Remova essas cópias do repositório e publique apenas o conteúdo de `dist/`. Assim, um deploy pelo Git não volta a publicar a versão antiga.

### HSTS

Ative o HSTS só depois de confirmar que o HTTPS funciona nas duas versões do domínio, com e sem `www`.

1. Defina `HOSTING_HSTS=true` no `.env`.
2. Rode `npm run build:apache` de novo.
