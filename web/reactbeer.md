# ReactBeer

Tässä itseopiskelumateriaalissa tehdään  Web-palvelinohjelmointi Ruby on Rails -kurssilta tutulle [ratebeer](https://github.com/mluukkai/WebPalvelinohjelmointi2017)-sovellukselle selaimessa toimiva frontend tällä hetkellä erittäin suosittua [React](https://facebook.github.io/react/)-kirjastoa käyttäen.

Materiaalissa tulee esiin myös muutamia oleellisia asioita, joita on huomioitava kun Railsia käytetään JSON-muotoista dataa tarjoavana API:na.

Materiaali toiminee parhaiten siten, että rakennat sovellusta itse samalla materiaalia seuraten.

## Tutustuminen Reactiin

Internet on täynnä eritasoisia React-tutoriaaleja ja sen takia tässä materiaalissa ei käydä perusteita läpi vaan oletetaan että opiskelet omatoimisesti Reactin perusteet.

Eräs hyvä tapa oppia perusteet on [Egghead.io](https://egghead.io):n kurssi [Start Using React to Build Web Applications](https://egghead.io/courses/react-fundamentals) joka koostuu noin 66 minuutista lyhyitä videoita. Videot kannattaa katsoa alusta videoon "Use map to Create React Components from Arrays of Data" asti. Seuraavat videot eivät ole kovin oleellisia, mutta viimeinen, eli "Debug React Components with Developer Tools in Chrome" kannattaa ehdottomasti katsoa.

Reactia ohjelmoidaan yleensä javascriptin uudella vesiolla [ES6](http://es6-features.org/#Constants):lla. Jos ES6 tuntuu oudolta, löytyy siihenkin Egghead.io:sta sopiva [kurssi](https://egghead.io/courses/learn-es6-ecmascript-2015)

## Projektin luominen

Luodaan projekti [create-react-app]() -sovellusta käyttäen ja käynnistetään sovellus komennolla `npm start` 

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

Itse sovellus siis rakennetaan komponentin App sisälle. Aloitetaan lisäämällä sovelluksellee navigaatiorakenne, jonka avulla valitaan se näkymä (esim. oluet), mikä sivulla näytetään.

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

Haluamme kuitenkin, että komponenteista on näkyvissä yksi kerrallaan. Määritellään sitä varten komponentin App tilaan avain _visible_, jonka arvo kontrolloi näytettävää alikomponenttia. Määritellään myös apumetodi, joka valitsee tilaan perustuen _render_-metodia varten oikean komponentin. 

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
Käytännössä vasta metodin kutsu palauttaa todellisen tapahtumankuuntelijafunktion:

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

Tehdään sovelluksen ulkoasusta tyylikkäämpi [Bootstrapin](http://getbootstrap.com) avulla. Käytetään [Reactstrap](https://reactstrap.github.io)-kirjastoa, joka paketoi bootstap-luokkia hyödyntävät html-elementit react-komponenteiksi.

Asenna ja ota kirjasto käyttöön [tämän sivun](https://reactstrap.github.io) kohdan _Adding Bootstrap_ ohjeiden mukaan.

Kuten sivulla mainitaan, tulee käyttävät reactstrap-komponentit importata sovellukseen.

Importataan nyt ainakin seuraavat:

```js
import {
  Collapse,
  Navbar,
  NavbarToggler,
  NavbarBrand,
  Nav,
  NavItem,
  NavLink,
  Container
} from 'reactstrap';
```

Muutetaan sitten Header-komponenttia seuraavasti:

```js
class Header extends React.Component {
  render(){
    return (
        <Navbar color="faded" light toggleable>
          <NavbarToggler right onClick={this.toggle} />
          <NavbarBrand href="/">reactbeer</NavbarBrand>
          <Collapse navbar>
            <Nav className="ml-auto" navbar>
              <NavItem>
                <NavLink href="#" onClick={this.props.beers}>Beers</NavLink>
              </NavItem>
              <NavItem>
                <NavLink href="#" onClick={this.props.styles}>Styles</NavLink>
              </NavItem>
              <NavItem>
                <NavLink href="#" onClick={this.props.login}>Login</NavLink>
              </NavItem>              
            </Nav>
          </Collapse>
        </Navbar>      
    )
  }
}
```

Muutetaan App-komponentin render-metodia vielä lisäämällä kaikki headerin ulkopuolella olevat komponentit reactstrap-komponentin _Container_ alle:

```js
    return (
      <div>
        <Header 
          beers={this.setVisible("BeersPage").bind(this)}
          styles={this.setVisible("StylesPage").bind(this)}
          login={this.setVisible("LoginPage").bind(this)}
        />
        <Container>
          {visiblePageComponent()}
        </Container>
      </div>
    );
```

Ohjeet reactstrap-komponenttien käyttöön löytyvät [täältä](https://reactstrap.github.io/components/)

## Oluttyylien hakeminen palvelimelta

Käynnistetään rails-sovellus oletusportin (jota react-fronend käyttää) sijaan porttiin 3001 antamalla komento `rails s -p 3001`.

Pääsemme käsiksi tyylien listaan json-muodossa osoitteessa <http://localhost:3001/styles.json>. Haetaan nyt json-muotoinen tyylien lista react-sovellukseen ja näytetään ne käyttäjälle HTML:ksi muotoiltuna.

React-sovelluksissa hyvänä käytäntönä on pitää suurin osa komponenteista tilattomana ja keskittää tila mahdollisimman ylhäällä hierarkiassa olevaan komponenttiin. Päätetäänkin tallentaa palvelimelta haettu tyylien lista komponenttiin _App_. Alustetaan tyylien lista App:in konstruktorissa tyhjäksi:

```js
class App extends Component {
  constructor() {
    super();
    this.state = {
      visible: "StylesPage",
      styles: []
    }
  }
  // ...
}
```

Hyvä paikka suorittaa tyylien hakeminen palvelimelta on metodi [componentWillMount](https://facebook.github.io/react/docs/react-component.html#componentwillmount) joka suoritetaan hieman ennen kuin komponentti renderöidään ensimmäistä kertaa.

Metodi käyttää modernien selaimien tukemaa [fetch-api](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):a tietojen hakemiseen. Metodi näyttää seuraavalta:


```js
  componentWillMount() {
    fetch('http://localhost:3001/styles.json')
     .then( response => response.json() )
     .then( results => {
        console.log(results)
        this.setState({
          styles: results
        })
     })
  }  
```
ensimmäinen _then_ parsii metodin json-muotoisen vastauksen ja toinen _then_ tulostaa ensin vastaanotetun listan konsoliin ja sijoittaa sen tilan avaimen _styles_ arvoksi.

Operaatio ei kuitenkaan toimi, selaimen konsoliin tulee seuraava virheilmoitus

![kuva](https://github.com/mluukkai/reactbeer/raw/master/img/reactbeer1.png)

Ongelman syy selitetään [täällä](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS) ja korjaus on siis sallia Rails-sovelluksessa cross-domain-pyynnöt.

Kyseessä on helppo operaatio gemin [rack-cors](https://github.com/cyu/rack-cors) asennus ja pieni muutos tiedostoon _application.rb_ (ks. linkki) riittää. Tehdään muutokset ja käynnistetään rails-sovellus uudelleen.

Nyt operaatio toimii ja vastaanotetut Style-oliot tulostuvat konsoliin.

Välitetään tyylien lista _StylesPage_-komponentille, eli muutetaan komponentit muodostava apumetodi seuraavaan muotoon:

```js
    let visiblePageComponent = () => { 
      if ( this.state.visible=="BeersPage" ) {
        return <BeersPage />
      } else if ( this.state.visible=="StylesPage" ) {
        return <StylesPage styles={this.state.styles}/>
      } else  {
        return <LoginPage />
      }     
    }
```

tyylisivu pääsee käsiksi välitettyyn oluiden listaan _propsien_ avulla. Laitetaan komponentti tulostamaan aluksi tyylien lukumäärä:

```js
class StylesPage extends React.Component {
  render(){
    let styles = this.props.styles
    return (
      <div>
        <h2>Styles</h2>  
        {styles.length}      
      </div>
    )
  }
}
```

Muutetaan sitten komponenttia siten, että se muodostaa taulukon, jonka rivit sisältävät tyylin nimen:

```js
class StylesPage extends React.Component {
  render(){
    let styles = this.props.styles
    return (
      <div>
        <h2>Styles</h2>  
        <Table striped>
          <thead>
            <tr>
              <th>name</th>
            </tr>
          </thead>
          <tbody>
            { styles.map(s => <tr><td>{s.name}</td></tr> ) }    
          </tbody>
        </Table>  
      </div>
    )
  }
}
```

Huomaa, että käytössä on reactstrap-komponentti [Table](https://reactstrap.github.io/components/tables/) joka siis on toimiakseen importattava.

Sivu toimii, mutta konsoliin tulee seuraava valitus:

![kuva](https://github.com/mluukkai/reactbeer/raw/master/img/reactbeer2.png)

Syy ongelmaan selviää [täältä](https://facebook.github.io/react/docs/lists-and-keys.html#keys).

Ongelmasta päästään eroon lisäämällä tyylin muodostavalle riville avaimeksi (_key_) tyylin id.

```js
{ styles.map(s => <tr key={s.id}><td>{s.name}</td></tr> ) } 
```

## const-elementti

Reactissa on hyvänä käytänteenä eristää kaikki loogiset kokonaisuudet omaksi komponentiksi. Tehdään nyt yksittäisen tyylin renderöinnistä huolehtiva komponentti. Koska komponentti on niin yksinkertainen, määrittelemme sen _const_- eli vakiokomponentiksi, joka teknisesti ottaen sisältää ainoastaan renderöinnin suorittavan metodin määrittelyn:

```js
const Style = (props) => 
  <tr>
    <td>{props.style.name}</td>
    <td>{props.style.description}</td>
  </tr> 
```

Const-komponenttia käytetään normaalin komponentin tapaan. _StylesPage_ muuttuu seuraavasti:

```js
class StylesPage extends React.Component {
  render(){
    let styles = this.props.styles
    return (
      <div>
        <h2>Styles</h2>  
        <Table striped>
          <thead>
            <tr>
              <th>name</th>
              <th>description</th>
            </tr>
          </thead>
          <tbody>
            { styles.map(s => <Style key={s.id} style={s}/> ) }    
          </tbody>
        </Table>  
      </div>
    )
  }
}
```

Reactbeerin tähänastinen koodi nähtävillä [täällä](https://github.com/mluukkai/reactbeer_code/tree/styles_page)

## Uuden oluttyylin lisääminen 

```js
```

```js
```

## Kirjautuminen palvelimelle

## Deployment


