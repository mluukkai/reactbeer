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

```js
```



