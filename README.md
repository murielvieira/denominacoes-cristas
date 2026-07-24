# Organograma interativo — denominações cristãs

## Como está estruturado

- `index.html` — o app completo (interface + lógica).
- `data.json` — os dados exibidos por padrão para quem acessa o site. **Este é um placeholder** com os 10 nós de exemplo que já vinham embutidos no HTML original. Troque por sua versão real assim que puder (veja abaixo).

## Como funciona o carregamento (já implementado no app, testado e confirmado)

1. **Visitante novo** (sem nada salvo no navegador dele) → o app busca `data.json` na mesma pasta do `index.html` e usa isso como versão padrão. Ele só *visualiza*; nada que ele fizer é enviado de volta pro GitHub — não tem como, é hospedagem estática.
2. Se ele clicar em **"Copiar e editar"** (aparece no banner de modo leitura), o app cria uma aba nova com uma cópia editável, salva **só no `localStorage` do navegador dele**. A partir daí ele edita livremente, sem afetar o que outras pessoas veem.
3. Mesmo que ele edite direto sem clicar em "copiar", o app salva a edição localmente do mesmo jeito (também sem tocar no `data.json` do GitHub).
4. Em visitas futuras, se já existir algo salvo no navegador dele, o app carrega essa versão local em vez de buscar o `data.json` de novo — ele não perde a edição.
5. Testei isso com dois "navegadores" simulados: um editou localmente, o outro continuou vendo exatamente o `data.json` publicado, sem contaminação entre os dois.

Ou seja: o requisito que você pediu — **GitHub = padrão pra todo mundo, ninguém edita pra todo mundo, mas cada um pode copiar e editar no próprio navegador** — já é exatamente o que o código faz. Eu só adicionei uma melhoria:

### Ajuste que eu fiz
Antes, se o `data.json` não carregasse (arquivo ausente, JSON quebrado, path errado), o app falhava **em silêncio** e mostrava os dados de exemplo sem avisar nada — difícil de debugar num deploy com problema. Agora ele mostra um toast de aviso (⚠ Não foi possível carregar data.json...) nesse caso, mas continua funcionando com os dados de exemplo em vez de quebrar.

## Como publicar no GitHub Pages

1. Crie um repositório novo (ou use um existente).
2. Suba `index.html` e `data.json` na raiz do repositório (ou dentro de `/docs`, se preferir usar essa opção do Pages).
3. Vá em **Settings → Pages** do repositório, escolha a branch (geralmente `main`) e a pasta (`/` ou `/docs`), salve.
4. O site fica disponível em `https://SEU-USUARIO.github.io/NOME-DO-REPO/` (leva alguns minutos para o primeiro deploy).

Não precisa configurar mais nada — o app já detecta sozinho a URL do `data.json` com base em onde o `index.html` está sendo servido (testei os cenários de página de projeto, página de usuário e domínio customizado).

## Como publicar a SUA versão real (substituindo o placeholder)

1. Abra o `index.html` no navegador onde você tem seus dados reais editados (localmente, ou publicado — tanto faz, contanto que seja a versão que você quer tornar o novo padrão).
2. Use o botão de exportar **"data.json"** que já existe na barra de ferramentas do app — ele baixa o estado atual (`G`) exatamente no formato que o app espera.
3. Substitua o `data.json` do repositório pelo arquivo baixado.
4. Commit + push. Em alguns minutos (cache do GitHub Pages) o novo padrão vale para todo visitante novo.

**Atenção:** visitantes que já editaram localmente (têm algo salvo no navegador deles) não vão ver essa atualização automaticamente — eles ficam na versão local deles até limparem os dados do site no navegador. Isso é intencional (não queremos apagar edição de ninguém sem avisar), mas é bom você saber.

## Testando localmente antes de publicar

O app **não busca `data.json` quando o host é `localhost` ou `127.0.0.1`** (feito de propósito, pra você poder abrir o arquivo local sem interferência). Se quiser testar o comportamento de "visitante novo buscando o data.json remoto" antes de publicar, sirva a pasta com um hostname que não seja localhost — por exemplo, adicionando uma entrada em `/etc/hosts` apontando algum nome fictício para `127.0.0.1`, ou simplesmente publicando num repositório de teste no GitHub Pages mesmo.
