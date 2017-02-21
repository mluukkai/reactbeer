# ReactBeer

React on noussut ...

## Tutustuminen reactiin

## Projektin luominen

Luodaan projekti [create-react-app]() sovellusta käyttäen ja käynnistetään sovellus komennolla `npm start` 

Poistetaan ylimääräiset tiedostot ja otetaan lähtökohdaksi seuraavat:

_public/index.html_

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>React App</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

_src/index.js_

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

_src/App.js_

```js
import React, { Component } from 'react';

class App extends Component {
  render() {
    return (
      <div>
        <h1>Reactbeer</h1>
      </div>
    );
  }
}

export default App;
```

## navigaatiorakenne

Itse sovellus siis rakennetaan komponentin App sisälle. Aloitetaan lisäämällä sovelluksellee navigaatiorakenne, jonka avulla valitaan se näkymä (esim. oluet) mikä sivulla näytetään.

Tehdän sovellukselle kolme yksinkertaista komponenttia (tehdään tässä vaiheessa kaikki tiedostoon _App.js_):

```js
class BeersPage extends React.Component {
  render(){
    return (
      <div>
        <h2>Beers</h2>
      </div>
    )
  }
}

class StylesPage extends React.Component {
  render(){
    return (
      <div>
        <h2>Styles</h2>
      </div>
    )
  }
}

class LoginPage extends React.Component {
  render(){
    return (
      <div>
        <h2>Login</h2>
      </div>
    )
  }
}
```

ja määritellään, että _App_ sisällyttää kaikki komponentit:

```js
class App extends Component {
  render() {
    return (
      <div>
        <h1>Reactbeer</h1>
        <BeersPage />
        <StylesPage />
        <LoginPage />
      </div>
    );
  }
}
```

Haluamme kuitenkin, että komponenteista on näkyvissä yksi kerrallaan. Määritellään sitä varten komponenttin App tilaan avain _visible_, joka arvo kontrolloi 
näytettävää alikomponenttia. Määritellään myös apumetodi, joka valitsee tilaan perustuen _render_-metodia varten oikean komponentin. 

```js
class App extends Component {
  constructor() {
    super();
    this.state = {
      visible: "BeersPage"
    }
  }

  render() {
    let visiblePageComponent = () => { 
      if ( this.state.visible=="BeersPage" ) {
        return <BeersPage />
      } else if ( this.state.visible=="BeersPage" ) {
        return <StylesPage />
      } else  {
        return <LoginPage />
      }
         
    }
  
    return (
      <div>
        <h1>Reactbeer</h1>
        {visiblePageComponent()}
      </div>
    );
  }
}
```

Nyt siis näkyvillä on oletustilan määrittelevä komponentti _BeersPage_. Tarvitsemme vielä tavan muuttaa tilaa. 
Tehdään sitä varten headeri, josta näytettävä sivu voidaan vaihtaa klikkaamalla. Tehdään headeri ensin suoraan komponenttiin App:


```js
class App extends Component {
  constructor() {
    super();
    this.state = {
      visible: "StylesPage"
    }
  }

  setVisible(componentName) {
    return (e) => {
      e.preventDefault()
      this.setState({
        visible: componentName
      })
    }
  }

  render() {
    let visiblePageComponent = () => { 
      if ( this.state.visible=="BeersPage" ) {
        return <BeersPage />
      } else if ( this.state.visible=="StylesPage" ) {
        return <StylesPage />
      } else  {
        return <LoginPage />
      }     
    }
  
    return (
      <div>
        <h1>Reactbeer</h1>
        <div>
          <a href="#" onClick={this.setVisible("BeersPage").bind(this)}>Beers</a>
          <a href="#" onClick={this.setVisible("StylesPage").bind(this)}>Styles</a>
          <a href="#" onClick={this.setVisible("LoginPage").bind(this)}>Login</a>
        </div>
        {visiblePageComponent()}
      </div>
    );
  }
```

Headerin linkeille on rekisteröity tapahtumankuuntelijat. Kuuntelijat generoidaan hieman erikoisella tavalla funktion _setVisible_ avulla.
Käytännössä metodin kutsu vasta palauttaa todellisen tapahtumankuuntelijafunktion:

```js
  setVisible(componentName) {
    return (e) => {
      e.preventDefault()
      this.setState({
        visible: componentName
      })
    }
  }
```

eli esim. linkkiin _Beers_ tulee tapahtumankäsittelijäksi funktio


```js
(e) => {
  e.preventDefault()
  this.setState({
    visible: "BeersPage"
  })
}
```

Sovelluksen navigaatiologiikka toimii nyt. Muutetaan vielä headeri omaksi komponentikseen:

```js
class Header extends React.Component {

  render(){
    return (
      <div>
        <a href="#" onClick={this.props.beers}>Beers</a>
        <a href="#" onClick={this.props.styles}>Styles</a>
        <a href="#" onClick={this.props.login}>Login</a>
      </div>
    )
  }
}
```

App-komponentti välittää tapahtumankuuntelijafunktiot Header-komponentille propseina:

```js
class App extends Component {
  // ...

  render() {
    let visiblePageComponent = () => { 
      // ...    
    }

    return (
      <div>
        <h1>Reactbeer</h1>
        <Header 
          beers={this.setVisible("BeersPage").bind(this)}
          styles={this.setVisible("StylesPage").bind(this)}
          login={this.setVisible("LoginPage").bind(this)}
        />
        {visiblePageComponent()}
      </div>
    );
  }
}
```

## Bootstrap




```js
```



