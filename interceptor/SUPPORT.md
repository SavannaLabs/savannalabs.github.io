# Suporte — Interceptor

Contacto de suporte: [SUPPORT_EMAIL]

Página de privacidade: [PRIVACY_POLICY_URL]

Este ficheiro é um modelo de página de suporte que pode ser publicado (por
exemplo em GitHub Pages). Vai ser usado como "Support URL" em App Store
Connect. Substitua os placeholders acima (`[SUPPORT_EMAIL]`,
`[PRIVACY_POLICY_URL]`, e `[USER_MANUAL_URL]`) pelos valores finais antes de
publicar.

## Como obter ajuda

1. Enviar um e‑mail para o endereço de suporte acima.
2. (Opcional) Abrir uma issue no repositório público do projeto — se optar por
   usar Issues, coloque um link permanente aqui.
3. Incluir, sempre que possível, um ficheiro exportado com as regras
   (`interceptor-rules.json`) e um pequeno conjunto de passos para reproduzir o
   problema.

## Passos rápidos de resolução (para o utilizador)

- Verifique se a extensão está activa: Safari → Settings → Extensions →
  Interceptor.
- Confirme que a regra relevante está ativada no popup do Interceptor.
- Recarregue a página e, se necessário, desligue/ligue a extensão nas
  preferências do Safari.
- Abra o menu Develop → Show JavaScript Console para ver mensagens e erros do
  content script / injected script.
- Se tiver um problema com export/import de regras, exporte-as e anexe o
  ficheiro ao report.

## O que incluir num relatório de bug (copiar/colar)

Por favor inclua as seguintes informações ao reportar um problema — quanto mais
detalhado, mais rápido é o diagnóstico:

- Versão do macOS (ex.: `macOS 13.5.2`). Pode obter com ` → About This Mac`.
- Versão do Safari (ex.: `Safari 17.0`).
- Versão do Interceptor e o número de build (se souber).
- Passos exactos para reproduzir o problema (o mínimo possível para reproduzir).
- URL de exemplo onde o problema ocorre (ou dizer que é um endpoint local
  tipo `http://localhost:3000`).
- Ficheiro exportado de regras (`interceptor-rules.json`) quando aplicável.
- Captura de ecrã do popup do Interceptor, e/ou do console de JavaScript com
  erros (ou copie/cole o texto do console).

Exemplo de snippet que um utilizador pode correr na consola para verificar se a
extensão está a interceptar `fetch` (útil também para revisão e suporte):

```js
fetch('https://jsonplaceholder.typicode.com/posts')
  .then(r => r.text()).then(console.log).catch(console.error)
```

Se o Interceptor estiver a funcionar com uma regra de exemplo, este `fetch`
deverá retornar o payload mock configurado.

## Instruções para enviar anexos

- Anexe o(s) ficheiro(s) exportados e captures de ecrã ao e‑mail ou à issue.
- Se enviar logs do console, prefira copiar/colar o texto em vez de imagens,
  para facilitar a pesquisa.

## Links úteis (substitua pelos URLs finais)

- Política de privacidade: [PRIVACY_POLICY_URL]
- Manual do utilizador / documentação: [USER_MANUAL_URL]
- Página do projecto / releases: [PROJECT_URL]

---

Se quiser que eu já preencha o e‑mail de suporte e as URLs (privacy / manual /
project), diga-me os valores e eu actualizo este ficheiro e faço commit.
