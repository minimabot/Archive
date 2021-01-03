## Proposal
- Use all React features without a class.
- Reuse stateful logic between components.
  -> Hooks lets you reuse stateful logic extracted out of components, test it separately and reuse it
     between different components without introducing the wrapper hell.
- Opt-in and 100% backwards-compatible.
- You can try Hooks in the 16.7 alpha.
- Don't rewrite your components!

## TLTR;
- useHook makes it possible to use state and have multiple states in a function component.
- A function component with hooks is easier to read, maintain than a class component.
- Unlike this.setState in a class, updating a state variable always replaces it instead of merging it.
- For React possibly to know which state variable corresponds to which useState call,
  React relies on not on names or anything but the order of these calls(useHooks). In order for this to work correctly,
  You cannot call hook inside a condition. It has to be at the top level of your component.
- It's possible to separate code not based on the life cycle method name but based on what the code is doing.
  You can pull some logic out of component and reduce duplication.
- useHook calls are just function calls and components are just functions. To share logic between two functions
  extract it to a different function.
- useHook gives you the flexibility to create your own abstractions.

## Readability
### A little bit long... :(
```js
import React from 'react';
import Row from './Row';

export default class Greeting extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: 'Mary',
    }
    this.handleNameChange = this.handleNameChange.bind(this);
  }
  
  handleNameChange(e) {
    this.setState({
      name: e.target.value
    });
  }
  
  render() {
    return(
      <section>
        <Row label="Name">
          <input
            value={this.state.name}
            onChange={this.handleNameChange}
          />
        </Row>
      </section>
    );
  }
}
```

### Easy to read, maintain and short! :)
```js
import React from 'react';
import Row from './Row';

export default function Greeting(props) {
  const [name, setName] = useState('Mary');
  
  function handleNameChange(e) {
    setName(e.target.value);
  }

  return (
    <section>
      <Row label="Name">
        <input
          value={name} // You don't need 'this.state'
          onChange={setName} // You don't need 'this'
        />
      </Row>
    </section>
  )
}
```

## State
### One object state and merging :(
```js
import React from 'react';
import Row from './Row';

export default class Greeting extends React.Component {
  constructor(props) {
    super(props);
    this.state = { // State's always object
      name: 'Mary',
      surname: 'Poppins',
    }
    this.handleNameChange = this.handleNameChange.bind(this);
    this.handleSurnameChange = this.handleSurnameChange.bind(this);
  }
  
  handleNameChange(e) {
    this.setState({
      name: e.target.value // This will merge name into the state object
    });
  }

  handleSurnameChange(e) {
    this.setState({
      surname: e.target.value
    });
  }
  
  render() {
    return(
      <section>
        <Row label="Name">
          <input
            value={this.state.name}
            onChange={this.handleNameChange}
          />
        </Row>
        <Row label="Surname">
          <input
            value={this.state.surname}
            onChange={this.handleSurnameChange}
          />
        </Row>
      </section>
    );
  }
}
```

### Mutiple states and replacing :)
```js
import React from 'react';
import Row from './Row';

export default function Greeting(props) {
  const [name, setName] = useState('Mary');
  const [surname, setSurname] = useState('Poppins'); // Hooks can be used more than once in a Component

  function handleNameChange(e) {
    setName(e.target.value);
  }

  function handleSurnameChange(e) {
    setSurname(e.target.value);
  }

  return (
    <section>
      <Row label="Name">
        <input
          value={name} // You don't need 'this.state'
          onChange={handleNameChange} // You don't need 'this'
        />
      </Row>
      <Row label="Surname">
        <input
          value={surname} // You don't need 'this.state'
          onChange={handleSurnameChange} // You don't need 'this'
        />
      </Row>
    </section>
  )
}
```

## Context
### Very explicit but nested :(
```js
import React from 'react';
import Row from './Row';
import { ThemeContext, LocaleContext } from './context';

export default class Greeting extends React.Component {
  constructor(props) {
    super(props);
    this.state = { // State's always object
      name: 'Mary',
      surname: 'Poppins',
    }
    this.handleNameChange = this.handleNameChange.bind(this);
    this.handleSurnameChange = this.handleSurnameChange.bind(this);
  }
  
  handleNameChange(e) {
    this.setState({
      name: e.target.value // This will merge name into the state object
    });
  }

  handleSurnameChange(e) {
    this.setState({
      surname: e.target.value
    });
  }
  
  render() {
    return(
      <ThemeContext.Consumer> // Render props. Wrapper hell... :(
        {theme => (
          <section className={theme}>
            <Row label="Name">
              <input
                value={this.state.name}
                onChange={this.handleNameChange}
              />
            </Row>
            <Row label="Surname">
              <input
                value={this.state.surname}
                onChange={this.handleSurnameChange}
              />
            </Row>
            <LocaleContext.Consumer>
              {locale => (
                <Row label="Language">
                  {locale}
                </Row>
              )}
            </LocaleContext.Consumer>
          </section>
        )}
      </ThemeContext.Consumer>
    );
  }
}
```

### Very explicit and also flat :)
```js
import React, { useState, useContext } from 'react';
import Row from './Row';
import { ThemeContext, LocaleContext } from './context';

export default function Greeting(props) {
  const [name, setName] = useState('Mary');
  const [surname, setSurname] = useState('Poppins'); // Hooks can be used more than once in a Component
  const theme = useContext(ThemeContext);
  const locale = useContext(LocaleContext);

  function handleNameChange(e) {
    setName(e.target.value);
  }

  function handleSurnameChange(e) {
    setSurname(e.target.value);
  }

  return (
    <section className={theme}>
      <Row label="Name">
        <input
          value={name} // You don't need 'this.state'
          onChange={handleNameChange} // You don't need 'this'
        />
      </Row>
      <Row label="Surname">
        <input
          value={surname} // You don't need 'this.state'
          onChange={handleSurnameChange} // You don't need 'this'
        />
      </Row>
      <Row label="Language">
        {locale}
      </Row>
    </section>
  )
}
```

## Lifecycle Method
### Reapeated(Same logic called in two places and remember to keep it consistent) :(
```js
import React from 'react';
import Row from './Row';
import { ThemeContext, LocaleContext } from './context';

export default class Greeting extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: 'Mary',
      surname: 'Poppins',
    }
    this.handleNameChange = this.handleNameChange.bind(this);
    this.handleSurnameChange = this.handleSurnameChange.bind(this);
  }
  
  componentDidMount() { // called when rendered
    document.title = this.state.name + ' ' + this.state.surname;
  }
  
  componentDidUpdate() { // called when updated
    document.title = this.state.name + ' ' + this.state.surname;
  }

  handleNameChange(e) {
    this.setState({
      name: e.target.value
    });
  }

  handleSurnameChange(e) {
    this.setState({
      surname: e.target.value
    });
  }
  
  render() {
    return(
      <ThemeContext.Consumer>
        {theme => (
          <section className={theme}>
            <Row label="Name">
              <input
                value={this.state.name}
                onChange={this.handleNameChange}
              />
            </Row>
            <Row label="Surname">
              <input
                value={this.state.surname}
                onChange={this.handleSurnameChange}
              />
            </Row>
            <LocaleContext.Consumer>
              {locale => (
                <Row label="Language">
                  {locale}
                </Row>
              )}
            </LocaleContext.Consumer>
          </section>
        )}
      </ThemeContext.Consumer>
    );
  }
}
```

### No duplicated :)
```js
import React, { useState, useContext, useEffect } from 'react';
import Row from './Row';
import { ThemeContext, LocaleContext } from './context';

export default function Greeting(props) {
  const [name, setName] = useState('Mary');
  const [surname, setSurname] = useState('Poppins');
  const theme = useContext(ThemeContext);
  const locale = useContext(LocaleContext);

  useEffect(() => {
    document.title = name + ' ' + surname; // No need 'this.state'. Accessible to any variables in a function scope. 
  })

  function handleNameChange(e) {
    setName(e.target.value);
  }

  function handleSurnameChange(e) {
    setSurname(e.target.value);
  }

  return (
    <section className={theme}>
      <Row label="Name">
        <input
          value={name}
          onChange={handleNameChange} // You don't need 'this'
        />
      </Row>
      <Row label="Surname">
        <input
          value={surname}
          onChange={handleSurnameChange} // You don't need 'this'
        />
      </Row>
      <Row label="Language">
        {locale}
      </Row>
    </section>
  )
}
```

## Side effect
### Unrelated mixed side effects :(
```js
import React from 'react';
import Row from './Row';
import { ThemeContext, LocaleContext } from './context';

export default class Greeting extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: 'Mary',
      surname: 'Poppins',
      width: window.innerWidth,
    }
    this.handleNameChange = this.handleNameChange.bind(this);
    this.handleSurnameChange = this.handleSurnameChange.bind(this);
    this.handleResize = this.handleResize.bind(this);
  }
  
  componentDidMount() { // unrelated logics. difficult to test these in isolation
    document.title = this.state.name + ' ' + this.state.surname;
    window.addEventListner('resize', this.handleResize);
  }
  
  componentDidUpdate() {
    document.title = this.state.name + ' ' + this.state.surname;
  }
  
  componentWillUnmount() { // Prevent memory leak
    window.removeEventListener('resize', this.handleResize);
  }

  handleResize () {
    this.setState({
      width: window.innerWidth
    })
  }

  handleNameChange(e) {
    this.setState({
      name: e.target.value
    });
  }

  handleSurnameChange(e) {
    this.setState({
      surname: e.target.value
    });
  }
  
  render() {
    return(
      <ThemeContext.Consumer>
        {theme => (
          <section className={theme}>
            <Row label="Name">
              <input
                value={this.state.name}
                onChange={this.handleNameChange}
              />
            </Row>
            <Row label="Surname">
              <input
                value={this.state.surname}
                onChange={this.handleSurnameChange}
              />
            </Row>
            <LocaleContext.Consumer>
              {locale => (
                <Row label="Language">
                  {locale}
                </Row>
              )}
            </LocaleContext.Consumer>
            <Row label="Width">
              <input
                {this.state.width}
              />
            </Row>
          </section>
        )}
      </ThemeContext.Consumer>
    );
  }
}
```

### Separate multiple side effects :)
```js
import React, { useState, useContext, useEffect } from 'react';
import Row from './Row';
import { ThemeContext, LocaleContext } from './context';

export default function Greeting(props) {
  const [name, setName] = useState('Mary');
  const [surname, setSurname] = useState('Poppins');
  const theme = useContext(ThemeContext);
  const locale = useContext(LocaleContext);

  useEffect(() => {
    document.title = name + ' ' + surname; // No need 'this.state'. Accessible to any variables in a function scope. 
  })

  const [width, setWidth] = useState(window.innerWidth);
  useEffect(() => { // Completely separate effect
    function handleResize = () => setWidth(window.innerWidth) // declared here because it is used only here
    window.addEventListener('resize', handleResize);
    return () => { // Conceptually cleaning up is is part of this effect
       window.removeEventListener('resize', handleResize);
    };
  })
  
  function handleNameChange(e) {
    setName(e.target.value);
  }

  function handleSurnameChange(e) {
    setSurname(e.target.value);
  }

  return (
    <section className={theme}>
      <Row label="Name">
        <input
          value={name}
          onChange={handleNameChange} // You don't need 'this'
        />
      </Row>
      <Row label="Surname">
        <input
          value={surname}
          onChange={handleSurnameChange} // You don't need 'this'
        />
      </Row>
      <Row label="Language">
        {locale}
      </Row>
      <Row label="Width">
        {width}
      </Row>
    </section>
  )
}
```

## Extraction(you can pull some logic out of component and reduce duplication) 
### Separate multiple side effects :)
```js
import React, { useState, useContext, useEffect } from 'react';
import Row from './Row';
import { ThemeContext, LocaleContext } from './context';

export default function Greeting(props) {
  const name = useFormInput('Mary');
  const surname = useFormInput('Poppins');
  const theme = useContext(ThemeContext);
  const locale = useContext(LocaleContext);
  const width = useWindowWidth();
  useDocumentTitle(name.value + ' ' + surname.value);
 
  return (
    <section className={theme}>
      <Row label="Name">
        <input {...name}/>
      </Row>
      <Row label="Surname">
        <input {...surname}/>
      </Row>
      <Row label="Language">
        {locale}
      </Row>
      <Row label="Width">
        {width}
      </Row>
    </section>
  )
}

function useFormInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  function handleChange(e) {
    setValue(e.target.value);
  }
  
  return {
    value,
    onChange: handleChange
  }
}

function useDocumentTitle(title) {
  useEffect(() => {
    document.title = name + ' ' + surname;
  }
}

function useWindowWidth() { // prefix 'use' convention very important for Lint. e.g. Warning hooks unconditionally
  const [width, setWidth] = useState(window.innerWidth);
  useEffect(() => { // Completely separate effect
    function handleResize = () => setWidth(window.innerWidth) // declared here because it is used only here
    window.addEventListener('resize', handleResize);
    return () => { // Conceptually cleaning up is is part of this effect
       window.removeEventListener('resize', handleResize);
    };
  });
  return width;
}
```

## Reference
[React Today and Tomorrow and 90% Cleaner React With Hooks](https://www.youtube.com/watch?v=dpw9EHDh2bM&feature=emb_logo)
