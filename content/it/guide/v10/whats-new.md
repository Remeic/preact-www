---
name: What's new in Preact X
description: 'Nuove funzionalità e cambiamenti in Preact X'
---

# Cosa c'è di nuovo in Preact Xxw

Preact X è un enorme step in avanti rispetto a Preact 8.x. Abbiamo ripensato ogni bit e byte del nostro codice e aggiunto un vastissimo numero di funzionalità nel mentre. 
La stessa cosa vale per i miglioramenti alla compatibilità per supportare ancor più librerie di terze parti.

In breve Preact X è quello che avremmo sempre voluto che Preact fosse, una libreria minuscola, veloce e ricca di funzionalità. E parlando di dimensioni, sarai felice di sapere che tutte le nuove funzionalità e il rendering migliorato hanno la stesso peso della versione "8.x"!

---

<div><toc></toc></div>

---

## Fragments

`Fragments` è una nuova delle nuovi importanti caratteristiche di Preact X ed è stato anche uno dei motivi per il quale abbiamo ripensato completamente l'archiettura di Preact.
Essi sono uno speciale tipo di componenti che renderizzano gli elementi figli in linea con il loro genitore, senza un componente extra che li racchiude entrambi. Inoltre essi ti permettono di ritornare più nodi da `render`.

[Documentazione Fragment →](/guide/v10/components#fragments)

```jsx
function Foo() {
  return (
    <>
      <div>A</div>
      <div>B</div>
    </>
  )
}
```

## componentDidCatch

Tutti desidereremmo che nella nostra applicazione non si verifichino errori, ma qualche volta succede. Con `componentDidCatch` è ora possibile rilevare e gestire eventuali errori che si verificano durante i metodi appartenenti al lifecycle come il metodo `render`, sono comprese anche le eccezzioni generate tutto l'abero dei componenti. Questo metodo può essere utilizzato per mostrare messaggi di errore più intuitivi e descrittivi per l'utente, o inviare un resoconto ad un servizio esterno nel caso qualcosa vada storto.

[Documentazione Lifecycle →](/guide/v10/components#componentdidcatch)

```jsx
class Catcher extends Component {
  state = { errored: false }

  componentDidCatch(error) {
    this.setState({ errored: true });
  }

  render(props, state) {
    if (state.errored) {
      return <p>SQualcosa è andato storto</p>;
    }
    return props.children;
  }
}
```

## Hooks

`Hooks` è un nuovo modo per semplificare la condivisione della logica fra componenti.
Essi rappresentano un alternativa alla già esistente API basata sulle classi. In preact essi si trovano dentro un addon, esso può essere importato attraverso `preact/hooks`

[Hooks Docs →](/guide/v10/hooks)

```jsx
function Counter() {
  const [value, setValue] = useState(0);
  const increment = useCallback(() => setValue(value + 1), [value]);

  return (
    <div>
      Counter: {value}
      <button onClick={increment}>Increment</button>
    </div>
  );
}
```

## createContext

L' API `createContext` è il vero successore di `getChildContext()`, mentre `getChildContext` va bene nel momento in cui sei davvero davvero sicuro di non modificare mai un valore, cade a pezzi non appena un componente, che si trova in mezzo al Provider, e il consumatore blocca un update attraverso `shouldComponentUpdate` quando restituisce `false`. Con la nuova context API questo problema fa parte del passato. É una vera soluzione pub/sub per fornire aggiornamenti in profondità nell'abero.

[Documentazione createContext →](/guide/v10/context#createcontext)

```jsx
const Theme = createContext('light');

function ThemedButton(props) {
  return (
    <Theme.Consumer>
      {theme => <div>Tema attivo: {theme}</div>}
    </Theme.Consumer>
  );
}

function App() {
  return (
    <Theme.Provider value="dark">
      <SomeComponent>
        <ThemedButton />
      </SomeComponent>
    </Theme.Provider>
  );
}
```

## Proprità personalizzate CSS (Custom Properties)

Ogni tanto le piccole cose fanno la differenza. Con i recenti progressi nel CSS puoi sfruttare [Variabili CSS](https://developer.mozilla.org/en-US/docs/Web/CSS/--*) per lo stile.

```jsx
function Foo(props) {
  return <div style={{ '--theme-color': 'blue' }}>{props.children}</div>;
}
```

## L'addon Compat è ora parte del core

Anche se siamo sempre stati desiderosi di aggiungere nuove funzionalità e spingere più avanti possibile Preact, il pacchetto `preact-compat` non ha ricevuto troppo amore, fino ad adesso ha vissuto in un repository separato rendo più difficile il coordinamento di grandi cambiamenti per il livello di compatibiltà e per Preact stesso. Spostando l'addon compact dentro a Preact stesso, non c'è bisogno di installare nessun pacchetto aggiuntivo per poter usufruire delle librerie provienienti dall'ecosistma di React.

Although we were always keen on adding new features and pushing Preact forward, the `preact-compat` package didn't receive as much love. Up until now it has lived in a separate repository making it harder to coordinate large changes spanning Preact and the compatibility layer. By moving compat into the same package as Preact itself, there's nothing extra to install in order to use libraries from the React ecosystem.

Il livello di compatibilità prende ora il nome di [preact/compat](/guide/v10/differences-to-react#features-exclusive-to-preactcompat), ed ha imparato diversi nuovi ed interessanti trucchi come `forwardRef`, `memo` ed innumerevoli miglioramenti per la compatibilità

```js
// Preact 8.x
import React from "preact-compat";

// Preact X
import React from "preact/compat";
```

## Molte correzioni di compatibilità 

Sono davvero tantissime per essere elencate, ma siamo cresciuti e abbiamo fatto passi da gigante sul fronte della comptibilità con le librerie proveniente dall'ecosistema React.
Ci siamo assicurati specificatamente di includere alcune delle più famose librerie all'interno del nostro processo di testing, così poter garantire a loro il nostro pieno supporto

Se per caso ti imbatti in una libreria che non ha funzionato come doveva con Preact 8, dovresti riprovare con X. È molto probabile che tutto funzioni come previsto;)

