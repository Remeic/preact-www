---
name: Tutorial
description: 'Scrivi la tua prima applicazione Preact'
---

# Tutorial

Questa guida ti guiderà nel costruire un semplice componente "Orologio Ticchettante". Informazioni più precise e dettagliate per ogni argomento possono essere trovate nelle loro pagine dedicate sotto al menù "Guida".

> :information_desk_person: Questa guida presuppone che tu abbia completato la sezione [Getting Started](/guide/v10/getting-started) e che tu abbia settato con successo i tool necessari. Nel caso non fosse così puoi partire da [preact-cli](/guide/v10/getting-started#best-practices-powered-with-preact-cli).

---

<div><toc></toc></div>

---

## Hello World

Senza nessuna configurazione aggiuntiva, ci sono due funzioni che vedrai sempre in qualsiasi codebase di Preact, e sono `h()` e `render()`. La prima funzione `h()` è utilizzata per trasformare JSX in una strutta comprensibile per Preact. Ma può essere utilizzato anche direttamente senza che JSX venga coinvolto:

```jsx
// With JSX
const App = <h1>Hello World!</h1>;

// ...the same without JSX
const App = h('h1', null, 'Hello World');
```

Questo codice da solo non fa niente e abbiamo bisogno di un metodo per inserire la nostra app Hello-World all'interno del DOM. Per questo dobbiamo usare la funzione `render()`.

```jsx
const App = <h1>Hello World!</h1>;

// Inject our app into the DOM
render(App, document.body);
```
Congratulazione, avete appena creato la vostra prima applicazione Preact!

## Hello World Interattiva

Renderizzare un testo è un inizio, ma noi vogliamo che la nostra applicazione sia un po' più interattiva. Vogliamo aggiornarla quando i dati cambiano. :star2:

Il nostro obbiettivo finale è avere un applicazione dove l'utente possa inserire il proprio nome ed esso venga mostrato quando il form è inviato. Per questo abbiamo bisogno di qualcosa dove possiamo memorizzare ciò che abbiamo inviato. Questo è il momento in cui i [Componenti](/guide/v10/components) entrano in gioco.

Trasformiamo quindi la nostra applicazione in un [Componenti](/guide/v10/components)

```jsx
import { h, render, Component } from 'preact';

class App extends Component {
  render() {
    return <h1>Hello, world!</h1>;
  }
}

render(<App />, document.body);
```

Noterai che abbiamo aggiunto un nuovo import `Component` all'inizio del file e che abbiamo trasformato `App` in una classe. Questa trasformazione da sola non è molto utile ma è un precursore per quello che andremo a fare successivamente. Per rendere le cose più interessanti aggiungeremo un gorm con un campo input e un bottone per l'invio.

```jsx
import { h, render, Component } from 'preact';

class App extends Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <form>
          <input type="text" />
          <button type="submit">Update</button>
        </form>
      </div>
    );
  }
}

render(<App />, document.body);
```

Ora stiamo parlando! Inizia ad assomigliare ad una vera applicazione! Dobbiamo ancora renderla interattiva. Ricorda che vogliamo cambiare `"Hello world!"` in `"Hello, [userinput]!"`, perciò abbiamo bisogno di conoscere il valore corrente del campo inpurt.

Memorizzeremo il valore del campo input in una speciale proprietà chiamata `state` del nostro Componente. È speciale perchè quando essa aggiornata attraverso il metodo `setState`, Preact non aggiorna solamente lo stato, ma pianifica anche una richiesta di rendering per questo componente. Una volta gestita la richiesta, il nostro componente verrà nuovamente renderizzato con lo stato aggiornato. 

Come ultima cosa abbiamo bisogno di collegare il nuovo stato con il campo imput impostando `value` e collegando un gestore di eventi all'evento `input`.

```jsx
import { h, render, Component } from 'preact';

class App extends Component {
  // Initialise our state. For now we only store the input value
  state = { value: '' }

  onInput = ev => {
    // This will schedule a state update. Once updated the component
    // will automatically re-render itself.
    this.setState({ value: ev.target.value });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <form>
          <input type="text" value={this.state.value} onInput={this.onInput} />
          <button type="submit">Update</button>
        </form>
      </div>
    );
  }
}

render(<App />, document.body);
```

A questo punto la nostra applicazione non dovrebbe essere cambiata poi molto dal punto di vista dell'utente, ma riuniremo tutti i pezzi nel prossimo step.

Aggiungeremo un gestore di eventi all'evento `submit` del nostro `<form>` in modo simile a quello che poco fa abbiamo fatto per il campo input. La differenza è che viene scritto in una proprietà differenta dal nostro  `state` chimata `name`.
Quindi cambiamo la nostra intestazione e inseriamo il nostro valore `state.name` lì.

```jsx
import { h, render, Component } from 'preact';

class App extends Component {
  // Add `name` to the initial state
  state = { value: '', name: 'world' }

  onInput = ev => {
    this.setState({ value: ev.target.value });
  }

  // Add a submit handler that updates the `name` with the latest input value
  onSubmit = ev => {
    // Prevent default browser behavior (aka don't submit the form here)
    ev.preventDefault();

    this.setState({ name: this.state.value });
  }

  render() {
    return (
      <div>
        <h1>Hello, {this.state.name}!</h1>
        <form onSubmit={this.onSubmit}>
          <input type="text" value={this.state.value} onInput={this.onInput} />
          <button type="submit">Update</button>
        </form>
      </div>
    );
  }
}

render(<App />, document.body);
```
Boom! Abbiamo finito! Possiamo insierie un nome, cliccare "Update" ed il nostro nome apparirà nella nostra intestazione

## Componente Orologio

Abbiamo scritto il nostro primo componente, facciamo ancora un po' di pratica. Questa volta costruiremo un orologio.

```jsx
import { h, render, Component } from 'preact';

class Clock extends Component {
  render() {
    let time = new Date().toLocaleTimeString();
    return <span>{time}</span>;
  }
}

render(<Clock />, document.body);
```

Ok, è stato fin troppo facile! Il problema è che l'ora non cambia, È congelato al momento in cui abbiamo eseguito il rendering del nostro componente orologio.

Quindi, vogliamo che un timer di 1 secondo inizi una volta che il componente viene aggiunto al DOM e si fermi se viene rimosso. Creeremo il timer e memorizzeremo un riferimento ad esso in `componentDidMount`, e fermeremo il timer in` componentWillUnmount`. Ad ogni tick del timer, aggiorneremo l'oggetto `state` del componente con un nuovo valore temporale. In questo modo verrà automaticamente eseguito il rendering del componente.

```jsx
import { h, render, Component } from 'preact';

class Clock extends Component {
  state = { time: Date.now() };

  // Called whenever our component is created
  componentDidMount() {
    // update time every second
    this.timer = setInterval(() => {
      this.setState({ time: Date.now() });
    }, 1000);
  }

  // Called just before our component will be destroyed
  componentWillUnmount() {
    // stop when not renderable
    clearInterval(this.timer);
  }

  render() {
    let time = new Date(this.state.time).toLocaleTimeString();
    return <span>{time}</span>;
  }
}

render(<Clock />, document.body);
```
L'abbiamo fatto ancora! Ora abbiamo un [orologio ticchettante](http://jsfiddle.net/developit/u9m5x0L7/embedded/result,js/)!
