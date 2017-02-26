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
        <a href="#" onClick={this.props.showBeers}>Beers</a>
        <a href="#" onClick={this.props.showStyles}>Styles</a>
        <a href="#" onClick={this.props.showLogin}>Login</a>
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
          showBeers={this.setVisible("BeersPage").bind(this)}
          showStyles={this.setVisible("StylesPage").bind(this)}
          showLogin={this.setVisible("LoginPage").bind(this)}
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
                <NavLink href="#" onClick={this.props.showBeers}>Beers</NavLink>
              </NavItem>
              <NavItem>
                <NavLink href="#" onClick={this.props.showStyles}>Styles</NavLink>
              </NavItem>
              <NavItem>
                <NavLink href="#" onClick={this.props.showLogin}>Login</NavLink>
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

## Tilaton komponentti

Reactissa on hyvänä käytänteenä eristää kaikki loogiset kokonaisuudet omaksi komponentiksi. Tehdään nyt yksittäisen tyylin renderöinnistä huolehtiva komponentti. 

Koska komponentti on erittäin yksinkertainen, määrittelemme sen _tilattomaksi komponentiksi_. Tilattomalla komponentilla ei ole tilaa (state), mutta se voi hyödyntää sille välitettyjä _propseja_. Yksinkertaisuutensa vuoksi tilaton komponentti voidaan määritellä funktiona, joka saa parametriksi propsit ja palauttaa näkymän:

```js
const Style = (props) => 
  <tr>
    <td>{props.style.name}</td>
    <td>{props.style.description}</td>
  </tr> 
```

Tilaton komponentti siis ikäänkuin määrittelee ainoastaan komponentin _render_-funktion.

Tilatonta komponenttia käytetään normaalin komponentin tapaan. _StylesPage_ muuttuu seuraavasti:

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

Reactbeerin tähänastinen koodi nähtävillä [täällä](https://github.com/mluukkai/reactbeer_code/tree/styles_page_v2)

## Uuden oluttyylin lisääminen 

Tehdään seuraavaksi lomake, jonka avulla käyttäjä voi lisätä uuden oluttyylin. Sijoitetaan lomake tyylien sivulle. Määritellään lomake omana komponenttina. Alustava versio seuraavassa:

```js
class NewBeerForm extends React.Component {
  render(){
    return (
      <div>
        <Form>
          <FormGroup>
            <Label for="exampleEmail">Name</Label>
            <Input type="text" name="name" id="name"/>
          </FormGroup>
          <FormGroup>
            <Label for="exampleText">Description</Label>
            <Input type="textarea" name="description" id="description"  />
          </FormGroup>
          <Button>Create style</Button>
        </Form>
      </div>
    )
  }
}
```

Sijoitetaan lomake tyylien sivun alalaitaan

```js
class StylesPage extends React.Component {
  render(){
    let styles = this.props.styles
    return (
      <div>
        <h2>Styles</h2>  
        <Table striped>
        ...
        </Table>  
        <NewBeerForm />
      </div>
    )
  }
}
```

Muutetaan lomaketta siten, että se ei ole oletusarvoisesti näkyvissä ja avautuu vasta kun käyttäjä klikkaa sivulle sijoitettavaa nappia. Näkyvyys hoidetaan lisäämällä lomakkeen tilaan _boolean_-arvoinen avain _visible_ jonka arvo määrittää sen näytetäänkö lomake vai lomakkeen avaava nappi.

```js
class NewBeerForm extends React.Component {
  constructor() {
    super();
    this.state = {
      visible: false
    }
  }

  toggleVisible() {
    this.setState({
      visible: !this.state.visible
    })
  }

  render(){
    let visibleComponent = () => { 
      if ( this.state.visible ) {
        return (
          <Form>
           <FormGroup>
            <Label for="name">Name</Label>
            <Input type="text" name="name" id="name"/>
          </FormGroup>
          <FormGroup>
            <Label for="description">Description</Label>
            <Input type="textarea" name="description" id="description"  />
          </FormGroup>
          <Button color="primary">Create style</Button>
          <Button color="warning" onClick={this.toggleVisible.bind(this)}>Cancel</Button>
        </Form>
        )
      } else {
        return <Button color="primary" onClick={this.toggleVisible.bind(this)}>Create new style</Button>
      }    
    }

    return (
      <div>
        {visibleComponent()}
      </div>
    )
  }
}
```

Lomakkeen avaavaan (ja sulkevaan) napiin on nyt lisätty tapahtumankuuntelijaksi funktio joka kääntää tilassa olevan avaimen _visible_ arvon.

Tehdään sitten tapahtumankäsittelijä joka huolehtii uuden tyylin luomisesta. Ensimmäinen versio on seuraava:


```js
  createStyle(e) {
    e.preventDefault()
    let form = e.target;

    let data = {
      name: form.elements['name'].value,
      description: form.elements['description'].value
    }

    form.elements['name'].value = ""
    form.elements['description'].value = ""

    console.log(data)
  }
```

Tapahtumankäsittelijä ei vielä lähetä palvelimelle mitään vaan ainoastaan tulostaa luotavan tyylin tiedot konsoliin ja sulkee lomakkeen.

Tapahtumankäsittelijä on sidottu luomisnapin sijaan lomakkeen tapahtumaan _onSubmit_:

```js
  let visibleComponent = () => { 
    if ( this.state.visible ) {
      return (
        <Form onSubmit={this.createStyle.bind(this)}>
          ...
        <Button color="primary">Create style</Button>
        <Button color="warning" onClick={this.toggleVisible.bind(this)}>Cancel</Button>
      </Form>
      )
    } else {
      return <Button color="primary" onClick={this.toggleVisible.bind(this)}>Create new style</Button>
    }    
  }
```

Täydennetään tapahtumankäsittelijä tekemään uuden tyylin luova HTTP POST -pyyntö palvelimelle:

```js
  createStyle(e) {
    e.preventDefault()
    let form = e.target;

    let data = {
      name: form.elements['name'].value,
      description: form.elements['description'].value
    }

    let request = {
      method: 'POST', 
      mode: 'cors',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(data)
    };

    fetch('http://localhost:3001/styles.json', request)
     .then( response => response.json() )
     .then( response => {
        console.log(response)
        this.toggleVisible()
     })

  }
```

Uuden tyylin luomisyritys johtaa kuitenkin virheeseen. Konsolissa näyttää seuraavalta:

![kuva](https://github.com/mluukkai/reactbeer/raw/master/img/reactbeer1.png)

Varsinainen syy selviää tutkimalla palvelimen virheilmoitusta:

```
Started POST "/styles.json" for ::1 at 2017-02-24 13:19:52 +0200
Processing by StylesController#create as JSON
  Parameters: {"name"=>"asd", "description"=>"asd", "style"=>{"name"=>"asd", "description"=>"asd"}}
Can't verify CSRF token authenticity
Completed 422 Unprocessable Entity in 2ms (ActiveRecord: 0.0ms)

ActionController::InvalidAuthenticityToken (ActionController::InvalidAuthenticityToken):
  actionpack (4.2.6) lib/action_controller/metal/request_forgery_protection.rb:181:in `handle_unverified_request'
```

Varsinainen syy selviää esim. [täältä](http://api.rubyonrails.org/classes/ActionController/RequestForgeryProtection.html). Kiertääksemme ongelman, meidän on siis disabloitava oletusarvoisesti päällä oleva CSRF tokenin tarkastus siinä tapauksessa jos palvelimelle lähtettävät pyynnöt sisältävät json-muotoista dataa. 

Operaatio onnistuu esim. muuttamalla ApplicationController-luokkaa seuraavasti:

```js
class ApplicationController < ActionController::Base
  protect_from_forgery unless: -> { request.format.json? }

  // ...
end
```

Tyylin luominen toimii nyt, mutta sovellus ei toimi aivan halutulla tavalla sillä vaikka uusi tyyli on luotu, ei sovelluksemme osaa renderöidä sitä. Oluttyylit ovat tallessa sovelluksen juurikomponentin _App_ tilassa, ja meidän pitäisi onnistua päivittää tilaa komponentin _NewBeerForm_ sisältä. Tämä onnistuu määrittelemällä juurikomponenttiin tyylin lisäävä funktio:


```js
class App extends Component {
  //...

  addStyle(style) {
    this.state.styles.push(style)
    this.setState({
      styles: this.state.styles
    })      
  }

  //...
}
```

ja välittämällä se ensin _StylesPage_-komponentille (propsina nimeltään _addStyle_): 

```js
class App extends Component {
  //...

  render() {
    let visiblePageComponent = () => { 
      if ( this.state.visible=="BeersPage" ) {
        return <BeersPage />
      } else if ( this.state.visible=="StylesPage" ) {
        return <StylesPage 
                styles={this.state.styles} 
                addStyle={this.addStyle.bind(this)}/>
      } else  {
        return <LoginPage />
      }     
    }

    // ...
  }
}
```

ja edelleen _StylesPage_-komponentilta _NewBeerForm_-komponentille:

```js
class StylesPage extends React.Component {
  render(){
    let styles = this.props.styles
    return (
      <div>
        <h2>Styles</h2>  
        <Table striped>
          // ..
        </div>Table>
        <NewBeerForm addStyle={this.props.addStyle}/>
      </div>
    )
  }
}  
```

ja nyt metodia voidaan kutsua HTTP POST -kutsun tekevän _fetch_:in takaisinkutsufunktiossa

```js

  createStyle(e) {
    // ...

    fetch('http://localhost:3001/styles.json', request)
     .then( response => response.json() )
     .then( response => {
        console.log(response)
        this.toggleVisible()
        this.props.addStyle(response)
     })

  }
```

Ja nyt kaikki tyylin lisäys toimii.

Kannattaa miettiä muutamaankin kertaan miten tilan päivittäminen hoidetaan alikomponenttiin välitettävän funktion avulla. Skenaario ei ole välttämättä ihan suoraviivainen!

Ratkaisumme on sikäli hieman ikävä, että se ei ilmoita käyttäjälle operaation onnistumisesta. Olisi ehkä aiheellista lisätä ratkaisuun esim. reactstrapin [alertina](https://reactstrap.github.io/components/alerts/) ilmoitus tyylin onnistuneesta luomisesta. Ratkaisumme ei myöskään nyt toimi hyvin jos tyylin luominen epäonnistuu esim. validointivirheen takia.

Jos tyylin luominen epäonnistuu, palauttaa palvelin statuskoodin 422 (unprocessable entity) ja validointiin liittyvän virheilmoituksen. 

Muutetaan metodia _createStyle_ siten, että koodi menee omaan _catch_-"haaraan" rekisteröityyn tapahtumankäsittelijään jos palvelin vastaa statuskoodilla, joka ei ole 201 (resource created):

```js
  createStyle(e) {
    // ...

    let ensureSuccess = (response) => { 
      if (response.status == 201)  {  
        return Promise.resolve(response)  
      } else {  
        return response.json().then( data => Promise.reject(data) ) 
      }  
    }

    fetch('http://localhost:3001/styles.json', request)
     .then( ensureSuccess )
     .then( response => { response.json } )
     .then( response => {
        console.log(response)
        this.toggleVisible()
        this.props.addStyle(response)
     }).catch( error => {
       console.log(error)
     }); 
  }
```

Virheenkäsittelystä vastaavassa callbackmetodissa ei nyt tehdä muuta kuin tulostetaan validointivirheestä kertova ilmoitus konsoliin: 

![kuva](https://github.com/mluukkai/reactbeer/raw/master/img/reactbeer4.png)

Virheilmoituksen näyttäminen web-sivulla jätetään harjoitustehtäväksi.

Reactbeerin tähänastinen koodi nähtävillä [täällä](https://github.com/mluukkai/reactbeer_code/tree/style_creation)

## Kirjautuminen palvelimelle

Tehdään sovellukseemme vielä lopuksi toiminnallisuus, jonka avulla käyttäjä pystyy autentikoitumaan, eli "kirjautumaan palvelimelle".

Toteutamme autentikoinnin ns. token-autentikointina, jonka periaatetta selitetään esim. [tässä videossa](https://www.youtube.com/watch?v=woNZJMSNbuo). 

Ideana autentikoinnissa on se, että käyttäjänimi (username) ja salasana (password) lähetetään frontendistä HTTP POST:in avulla osoitteeseen palvelimelle.

Jos autentikointi onnistuu, palauttaa palvelin _tokenin_, jos ei, palauttaa palvelin HTTP statuskoodin 422 (Unprocessable Entity).

Onnistuneen autentikoinnin yhteydessä frontend tallettaa tokenin ja liittää sen mukaan kaikkiin palvelimelle meneviin pyyntöihin. 

Jos palvelimelle tehdään pyyntö, joka on sallittu vain kirjautuneille käyttäjille, palvelin tarkastaa liittyykö pyyntöön validi token
jos ei, vastaa palvelin HTTP statuskoodilla 401 (unauthorized).

Kun käyttäjä loggaa ulos sovelluksesta, pyytää fronend palvelinta mitätöimään tokenin.
Mitätöinti voi tapahtua myös kuluneen ajan perusteella.
Sovelluksemme backend olettaa, että AT on liitetty pyynnön auth-token headeriin.

Toteuttaaksemme token-autentikaation tarvitsemme muutoksia myös palvelimelle. Aloitetaan niistä.

Listätään route kirjautumista varten

```ruby
post 'login_api', to: 'sessions#login_api' 
```

ja sitä vastaavaan kontrollerimetodin alustava, tässä vaiheessa kovakoodatun tokenin palauttava versio:

```ruby
class SessionsController < ApplicationController
  # ...

  def login_api
    user = User.find_by username: params[:username]
    if user and user.authenticate(params[:password]) 
      render json: { token: 'salainen_token' }
    else
      render json: { error: "invalid username or pasword " }, status: :unprocessable_entity
    end
  end

  # ...
end
```

Tehdään seuraavaksi lomake komponenttiin _Login_:


```js
  render(){
    return (
      <div>
        <Form onSubmit={this.login.bind(this)}>
          <FormGroup>
            <Label for="username">username</Label>
            <Input type="text" name="username" id="username" />
          </FormGroup>
          <FormGroup>
            <Label for="password">password</Label>
            <Input type="password" name="password" id="password" />
          </FormGroup>
          <Button color="primary">Login</Button>
        </Form>
      </div>
    )
  }
```

ja kirjautumisesta huolehtiva callback-metodi:

```js
class LoginPage extends React.Component {
  login(e) {
    e.preventDefault()
    let form = e.target;

    let data = {
      username: form.elements['username'].value,
      password: form.elements['password'].value
    }

    let request = {
      method: 'POST', 
      mode: 'cors',
      headers: {
        'Content-Type': 'application/json',
        'Accept': 'application/json'
      },
      body: JSON.stringify(data)
    };

    let ensureSuccess = (response) => { 
      if (response.status == 200) {  
        return Promise.resolve(response)  
      } else {  
        return response.json().then( data => Promise.reject(data) ) 
      }  
    }

    fetch('http://localhost:3001/login_api', request)
     .then( ensureSuccess )
     .then( response => response.json() )
     .then( response => {
        console.log(response)
        window.r = response
        form.elements['username'].value = ""
        form.elements['password'].value = ""   
        this.props.setTokenAndUser(response.token, data.username)
     }).catch( error => {
       console.log(error)
     });   
  }
```

Metodi on pienin muutoksin copypastettu tyylin luovasta metodista. Operaation onnistuessa metodi käyttää komponentille propseina välitettyä komponentin _App_ tilaa muuttavaa metodia _setTokenAndUser_ joka tallettaa tokenin ja käyttäjätunnuksen _App_-komponentin tilaan:

```js
class App extends Component {
  // ...

  setTokenAndUser(token, username) {
    this.setState({
      token: token,
      username: username,
      visible: 'WelcomePage'
    })      
  }

  // ...

  render() {
    let visiblePageComponent = () => { 
      if ( this.state.visible=="BeersPage" ) {
        return <BeersPage />
      } else if ( this.state.visible=="StylesPage" ) {
        return <StylesPage 
                styles={this.state.styles} 
                addStyle={this.addStyle.bind(this)}/>
      } else if ( this.state.visible=="LoginPage" )  {
        return <LoginPage setTokenAndUser={this.setTokenAndUser.bind(this)}/>
      } else {
        return <WelcomePage username={this.state.username} />
      }      
    }

    // ...
  }      
}
```

Kirjautuminen päättyy yksinkertaisen _WelcomePage_-komponentin renderöimiseen:

```js
const WelcomePage = (props) => 
  <Alert color="success">
    Welcome back {props.username}
  </Alert> 
```

## Autentikoitu pyyntö

Muutetaan nyt tyylien luomista siten, että palvelin sallii luomisen ainoastaan jos pyyntö on autentikoitu, eli sisältää autentikaatiotokenin. Päätetään välittää autentikaatiotoken headerin _Authorization_ arvona. Tehdään palvelimelle seuraava muutos:

```ruby
class StylesController < ApplicationController
  # ...

  def create
    @style = Style.new(style_params)

    if request.format.json?
      user = User.get_by_token(request.headers["Authorization"])
      if user.nil?
        return render json: { error: "no valid token in request" }, status: :unauthorized
      end
    end
    
    respond_to do |format|
      if @style.save
        format.html { redirect_to @style, notice: 'Style was successfully created.' }
        format.json { render :show, status: :created, location: @style }
      else
        format.html { render :new }
        format.json { render json: @style.errors, status: :unprocessable_entity }
      end
    end
  end
end
```

Eli siinä tapauksessa että pyynnön datan formaatti on json (eli pyyntö ei tule railsin generoimasta templatesta) otetaan pyynnön headerista token komennolla <code>request.headers["Authorization"]</code> ja kysytään luokkaan <code>User</code> toteutetulta metodilta mitä käyttäjää token vastaa. Jos metodi palauttaa nil eli tokenia ei ollut tai kyseessä ei ollut validi token, palautetaan virheilmoitus. Jos token oli validi, jatketaan pyynnön käsittelyä ja luodaan uusi tyyli.

Luokan <code>User</code> metodi <code>get_by_token</code> toimii nyt siten, että jos token on  kovakoodattu _salainen_token_, palautetaan ensimmäinen käyttäjä, muussa tapauksessa _nil_:

```ruby
class User < ActiveRecord::Base
  # ...

  def self.get_by_token(token)
    return User.first if token=='salainen_token'
    nil
  end

  # ...
end
```

Kun nyt kokeilemme uuden tyylin luomista, se ei toimi sillä vaikka olemme kirjautuneena, ei tokenia vielä lisätä pyyntöön:

![kuva](https://github.com/mluukkai/reactbeer/raw/master/img/reactbeer5.png)

Eli lisätään token pyynnön headerin _Authorization_ arvoksi. Komponenttiin _NewBeerForm_ tarvitaan ainoastaan pieni lisäys:

```js
class NewBeerForm extends React.Component {
   // ...

  createStyle(e) {
    // ...

    let request = {
      method: 'POST', 
      mode: 'cors',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': this.props.token,
      },
      body: JSON.stringify(data)
    };

  // ...  
  }

  // ... 
}
```

Komponentille pitää siis välittää token propsien avulla. Token on komponentin _App_ tilassa, eli se pitää välittää komponentin _StylesPage_ kautta:

```js
class StylesPage extends React.Component {
  render(){
    let styles = this.props.styles
    return (
      <div>
        <h2>Styles</h2>  
        <NewBeerForm addStyle={this.props.addStyle} token={this.props.token}/>
        <Table striped>
          // ...
        </Table>  
      </div>
    )
  }
}          
```

ja komponenttiin _App_ tarvittavat muutokset:

```js
class App extends Component {
  constructor() {
    super();
    this.state = {
      visible: "LoginPage",
      styles: [],
      username: '',
      token: ''
    }
  }

  // ...

  render() {
    let visiblePageComponent = () => { 
      console.log(this.state.visible)
      if ( this.state.visible=="BeersPage" ) {
        return <BeersPage />
      } else if ( this.state.visible=="StylesPage" ) {
        return <StylesPage 
                styles={this.state.styles} 
                addStyle={this.addStyle.bind(this)}
                token={this.state.token}/>
      } else if ( this.state.visible=="LoginPage" )  {
        return <LoginPage setTokenAndUser={this.setTokenAndUser.bind(this)}/>
      } else {
        return <WelcomePage username={this.state.username} />
      }    
    }

    // ...
  }
}    
```

Reactbeerin lopullinen koodi nähtävillä [täällä](https://github.com/mluukkai/reactbeer_code/tree/login)

## Palvelimen viimeistely

Muutetaan nyt vielä palvelinta siten, että kovakoodatun tokenin sijaan luodaan käyttäjäkohtainen token, joka talletetaan tietokantaan _users_-tauluun.

Ainoa mutos kontrollerien tasolla on seuraava:

```ruby
class SessionsController < ApplicationController
  # ...

  def login_api
    user = User.find_by username: params[:username]
    if user and user.authenticate(params[:password]) 
      render json: { token: user.get_token }
    else
      render json: { error: "invalid username or pasword " }, status: :unprocessable_entity
    end
  end

  # ...
end
```

eli palautetaan _user_-olion kertoma token.

Lisätään _users_-tauluun sarake tokenin tallettamiseen seuraavan migraation avulla:

```ruby
class AddTokenToUser < ActiveRecord::Migration
  def change
    add_column :users, :token, :string
  end
end
```  

Luokkaan _User_ tarvittavat muutokset ovat seuraavat:

```ruby
class User
  # ...

  def self.get_by_token(token)
    User.find_by token: token
  end

  def generate_token
    begin
      token = SecureRandom.hex
    end while User.get_by_token(token)
    
    update_attribute :token, token
  end

  def get_token
    generate_token unless token
    token
  end

  # ...
end
```  

Metodi <code>get_token</code> siis palauttaa käyttäjän tokenin, jos tokenia ei vielä ole se generoidaan tokenin uniikkiuden varmistavalla metodilla <code>generate_token</code>.

Nyt sovelluksemme toimii kaikilla backendiin määritellyillä käyttäjätunnuksilla.

## Deployment

Deployaa _rails backendin_ uusin versio Herokuun ja vaihda frontendisi käyttämään localhostin sijaan Herokussa olevaa backendia.

Saat deployattua forntendin helpoiten Herokuun noudattamalla
[tätä](https://github.com/mars/create-react-app-buildpack) ohjetta.

## Pro tips

### debuggaus

Javascriptin debuggaus ei ole välttämättä helppoa. Selaimen javascript-konsolin aktiivinen käyttö on tietysti kaiken lähtökohta. **Konsolin tulee olla ohjelmoidessa koko ajan auki**. Arvailun sijaan kannattaa tulostella asioita konsoliin komennolla _console.log_.

Voit myös pysäyttää koodin mihin kohtaa tahansa kirjoittamalla koodiin komennon _debugger_. Tällöin pääset tutkimaan konsolista komponenttien tilaa ym. Käytännössä _debugger_ toimii hyvin samaan tapaan kuin Railsin debuggeri.

Chromen lisäosana toimiva [react developer tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi) on hyvä apuväline debuggaukseen. Sen avulla on mahdollista tarkkailla komponentteja ja niiden tilaa selaimen developer konsolista: 

![kuva](https://github.com/mluukkai/reactbeer/raw/master/img/reactbeer6.png)

Kun olet tekemässä pyyntöjä backendiin, kannattaa ehdottomasti ensin kokeilla pyyntöjen toimivuutta [postmanilla](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) ja kutsua backendia omasta koodista vasta siinä vaiheessa kun olet varma, että tiedät minkälaisia headereja ym. backend odottaa.

### Komponenttien ja sovelluslogiikan eristäminen moduuleihin

Koodissamme on muutamiakin ongelmia. Ensinnäkin kaikki komponentit on kirjoitettu samaan tiedostoon. Reactia kirjoitettaessa on tapana tehdä jokainen komponentista moduuli ja sijoittaa se omaan tiedostoonsa hakemistoon _src/components_. Modulissa määriteltyä komponenttia käytettäessä se on ensin importoitava. Ks. lisää modulien [importtaamisesta ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) [eksporttaamisesta](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)

Olemme kirjoittaneet palvelimelle HTTP-pyyntöjä tekevän koodi (funktio _fetch_) suoraan näkymäkomponentteihin. Kyseessä ei tietenkään ole järkevä tapa, se rikkoo mm. single responsibility -periaatetta. Palvelimen kanassa keskusteleva koodi on myös järkevä eriyttää omaksi modulikseen.

Osittain refakotoroitu koodi nähtävillä [täällä](https://github.com/mluukkai/reactbeer_code/tree/refactoring). Koodissa ainoastaan lääkomponentit (esim. _StylesPage_) on eroteltu omiin moduuleihinsa, ainoastaan yhden komponentin käyttäät alikomponentit (esim. _Style_) on määritelty pääkomponentin kanssa samassa moduulissa.

### Reactia laajentavat kirjastot

Kun perusreact on hallinnassa, kannattaa tutustua seuraaviin:

* [Redux](http://redux.js.org/docs/introduction/)
* [ReactRouter](https://github.com/reactjs/react-router-tutorial)
* [Flow](https://flowtype.org)
