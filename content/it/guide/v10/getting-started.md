---
name: Getting Started
description: "Come iniziare con Preact. Impareremo a come impostare gli strumenti (se necessari) e iniziare a scrivere un applicazione"
---

# Iniziamo

Questa guida ti aiuterà ad iniziare a sviluppare un'applicazione Preact

Indichiamo qua le 3 più popolari opzioni. Se non conosci ancora bene Preact, ti consigliamo di partire con [Preact CLI](#best-practices-powered-by-preact-cli).

---

<div><toc></toc></div>

---

## Senza strumenti di compilazione

Preact è confezionato per essere utilizzato direttamente nel browser, e non richiede nessun tipo di build o di strumento:

```html
<script type="module">
  import { h, Component, render } from 'https://unpkg.com/preact?module';

  // Crea la tua applicazione
  const app = h('h1', null, 'Hello World!');

  render(app, document.body);
</script>
```

[🔨 Modificalo su Glitch](https://glitch.com/~preact-no-build-tools)

Il principale svantaggio dello sviluppare in questo modo è la mancanza di JSX, esso infatti richiede uno step di compilazione, una valida e performante alternativa a JSX è documentata nella prossima sezione

### Alternative a JSX

Invocare direttamente `h` o `createElement` può risultare noioso. JSX ha il vantaggio di sembrare simile all'HTML, il che lo rende più facile da capire per molti sviluppatori.
JSX richiede tuttavia uno step di compilazione, quindi consigliamo vivamente un'alternativa chiamata [HTM][htm].

[HTM][htm] è una sinstassi simile a JSX che funziona attraverso Javascript standard.
Invece di richiedere un qualsiasi step di compilazione, esso usa i [Tagged Templates](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#Tagged_templates) di JavaScript, aggiunti nel 2015 e sono supportati in [tutti i moderni browser](https://caniuse.com/#feat=template-literals).
Questo metodo è sempre più diffuso per scrivere applicazioni Preact, dato che ci sono meno parti da comprendere rispetto ad una tradizionale configurazione degli strumenti di compilazione.

```html
<script type="module">
  import { h, Component, render } from 'https://unpkg.com/preact?module';
  import htm from 'https://unpkg.com/htm?module';

  // Inizializza html con Preact
  const html = htm.bind(h);

  function App (props) {
    return html`<h1>Hello ${props.name}!</h1>`;
  }

  render(html`<${App} name="World" />`, document.body);
</script>
```

[🔨 Modificalo su Glitch](https://glitch.com/~preact-with-htm)

> **Consiglio:** HTM fornisce anche un conveninente import di Preact:
>
> `import { html, render } from 'https://unpkg.com/htm/preact/index.mjs?module'`

Per maggiorni informazioni su HTM, consulta la sua [documentazione][htm].


## Best practices basate su Preact CLI

[Preact CLI] è una soluzione standard per la creazione di applicazioni Preact, ottimizzata per il moderno sviluppo web. Basato su strumenti standard come Webpack, Babel e PostCss. Preact CLI non richiede nessun tipo di configurazione o una conoscenza preliminare per partire, la sua semplicità la rende il modo più popolare di usare Preact.

Come indica il nome, Preact CLI è un **c**ommand-**li**ne tool che può essere eseguite nel terminale nel tuo computer, installalo globalmente eseguendo: 

```bash
npm install -g preact-cli
```

Dopo di ciò, avrai disponibile un nuovo comando nel tuo terminale chiamato `preact`. Usandolo, puoi create una nuova applicazione lanciando il comando `preact create`:

```bash
preact create default my-project
```

Verrà creata una nuova applicazione basata sul [template di default](https://github.com/preactjs-templates/default). Ti verranno chieste alcune informazioni riguardo al tuo progetto, che verrà generato nella cartella da te specificata (`my-project` in questo caso).

> **Consilgio:** Ogni repository su GitHub con una cartella `template/` può essere usato come template custom
>
> `preact create <username>/<repository> <project-name>`

### Prepararsi per lo sviluppo

Ora siamo pronti per iniziare la nostra applicazione. Per far partire un server di sviluppo, è necessario eseguire questi comandi dentro alla cartella del progetto appena creata (`my-project` in questo caso):

```bash
# Spostiamoci nella cartella del progetto generata
cd my-project

# Facciamo partire il server di sviluppo
npm run dev
```
Una volta che il server è partito, esso stamperà sullo schermo una URL locale per lo sviluppo, quest'ultima va aperta nel tuo browser.
Ora sei davvero pronto per iniziare a scrivere il codice della tua applicazione!

### Produrre una build per la produzione

Arriva il momento nel quale hai bisogno di distribuire la tua applicazione da qualche parte.
La CLI fornisce l'utilissimo comando `build` che genererà una build di produzione altamente ottimizzata della tua applicazione.

npm run build

```

Al termine, avrai una nuova cartella `build/` la quale può essere distribuita direttamente sul server.

> Per la lista di tutti i comandi disponibili, consulta la [Documentazione di Preact CLI](https://github.com/preactjs/preact-cli#cli-options).

## Integrazione in una pipeline esistente

Se hai già impostato una pipeline di strumenti, è molto probabile che essa comprenda un bundler.
Le scelte più popolari sono webpack](https://webpack.js.org/), [rollup](https://rollupjs.org) or [parcel](https://parceljs.org/). Preact funziona direttamente e senza modifiche con tutti loro!

### Impostare JSX

Per transpilare JSX, hai bisogno del Plugin Babel che lo converta in codice JavaScript valido. Quello più comunemente usato è [@babel/plugin-transform-react-jsx](https://babeljs.io/docs/en/babel-plugin-transform-react-jsx). Una volta installato, è necessario specificare la funzione per JSX che dovrebbe essere utilizzata:

```json
{
  "plugins": [
    ["@babel/plugin-transform-react-jsx", {
      "pragma": "h",
      "pragmaFrag": "Fragment",
    }]
  ]
}
```

> [Babel](https://babeljs.io/) ha una delle migliori documentazione. Raccomandiamo altamente di consultarla per tutte le questioni legate a Babel e come configurarlo.

### Aliasing React to Preact

Ad un certo punto, tu probabilmente vorrai usare il vasto ecosistema di React.
Librerie e Componenti originariamente scritti per React funzionani senza problemi grazie al nostro livello di compatibilità. Per poterlo usare, dobbiamo puntare tutte le importazioni di `react` e` react-dom` su Preact. Questo passaggio è chiamato  _aliasing._

#### Aliasing in webpack

Per creare un alias di qualsiasi pacchetto in webpack, è necessario aggiungere la sezione `resolve.alias`
alla tua configurazione. A seconda della configurazione che stai utilizzando, questa sezione potrebbe
essere già presente, ma mancano gli alias per Preact. 

```js
const config = { 
   //...snip
  "resolve": { 
    "alias": { 
      "react": "preact/compat",
      "react-dom/test-utils": "preact/test-utils",
      "react-dom": "preact/compat",
     // Must be below test-utils
    },
  }
}
```

#### Aliasing in Parcel

Parcel usa lo standard `package.json` file per leggere la configurazione delle opzioni con la chiave `alias`.

```json
{
  "alias": {
    "react": "preact/compat",
    "react-dom/test-utils": "preact/test-utils",
    "react-dom": "preact/compat"
  },
}
```

#### Aliasing in Rollup
Per creare un alias con Rollup, hai bisogno di installare [@rollup/plugin-alias](https://github.com/rollup/plugins/tree/master/packages/alias).
Il plugin dovrà essere posizionato prima del [@rollup/plugin-node-resolve](https://github.com/rollup/plugins/tree/master/packages/node-resolve), questo passaggio è molto importante.


```js
import alias from '@rollup/plugin-alias';

module.exports = {
  plugins: [
    alias({
      entries: [
        { find: 'react', replacement: 'preact/compat' },
        { find: 'react-dom', replacement: 'preact/compat' }
      ]
    })
  ]
};
```

#### Aliasing in Jest

[Jest](https://jestjs.io/) consente la riscrittura dei percorsi dei moduli simili ai bundler.
Queste riscritture vengono configurate utilizzando espressioni regolari nella configurazione di Jest:

```json
{
  "moduleNameMapper": {
    "^react$": "preact/compat",
    "^react-dom/test-utils$": "preact/test-utils",
    "^react-dom$": "preact/compat"
  }
}
```

[htm]: https://github.com/developit/htm
[Preact CLI]: https://github.com/preactjs/preact-cli

## Configurazione TypeScript preact/compat

Your project could need support for the wider React ecosystem.  To make your application
compile, you'll need to disable type checking on your `node_modules` like this.  This way,
your alias will work properly when libraries import React.

Il tuo progetto potrebbe aver bisogno di supporto per il più ampio ecosistema React. Per far si che la tua applicazione sia compilabile,  dovrai disabilitare il controllo del tipo sui tuoi `node_modules` in questo modo. In questo modo,il tuo alias funzionerà correttamente quando le librerie importeranno React. 

```json
{
  ...
  "skipLibCheck": true,
}
```
