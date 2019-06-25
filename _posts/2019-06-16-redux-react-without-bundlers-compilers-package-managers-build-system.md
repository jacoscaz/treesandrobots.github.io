---
title: "React without bundlers, compilers and package managers"
description: "Making front-end development a little less complicated"
---

_TL;DR - there are ways to make `React` applications without build systems.
Scroll to the bottom of this post for a example of such an application.
Modern frontend development is a little crazy._

I've recently had a chance to peruse the world of modern frontend development,
spending some time getting acquainted with `React` and its functional approach
to managing UIs.

Although it had been years since my previous foray into serious frontend 
coding, I was not prepared for how _complicated_ frontend development has 
become.

Back then I used to work on single-page applications built with libraries like
`underscore`, `backbone` and a templating engine such as `doT`. The concept of 
modular JavaScript was a relatively new, hot topic and tools such as 
`require.js` were starting to bring the concept of _bundling_ to the masses.
Hip colleagues had just started transitioning to `lodash`, `bower` had recently
appeared on the horizon and we were all beginning to see some early indications
of what a post-`jQuery` world would look like. `Ext.js` was making strides in
the corporate world.

From a methodology perspective, we would generally build applications 
_from the ground up_, often starting from a single `.html` file and introducing
new dependencies as needed in order to sustain the growing complexity of our 
products. Dependencies were generally hand-picked and well-understood in both 
their scope and inner workings. Build systems were rarely used and almost 
solely for production releases.

What a stark contrast to today's approach!

Most of the modern frontend development methodology assumes the presence of, at
the very least, a build system comprised of the following components:

- package managers (`NPM`, `Yarn`);
- compilers / transpilers (`Babel`, `node-scss`);
- bundlers (`Webpack`, `Rollup`).

Developing competence in each of these tools is, in itself, a non-trivial
effort. In fact, even the [official React tutorial](https://reactjs.org/tutorial/tutorial.html#prerequisites)
doesn't teach developers how to manage their own development environment
but merely suggest the use of an additional tool, `create-react-app`, to
automate such management.

Imagine my surprise when I found out that, as of today, bootstrapping a project
with `create-react-app` introduces `4211` dependencies. 
__Four-thousand-two-hundred-eleven__! And this is _before_ adding any 
project-related dependency.

This is __insane__, if only for the long-term stability of the resulting
codebase. Furthermore, it's even more insane considering that there __are__ 
alternatives. Let's see:

For __package management__, a CDN such as `unpkg.com` makes it trivial to
incorporate any package published on `NPM` using `<script>` tags. If 
referencing an external CDN is not an option, source files can simply be 
shipped as a part of the application.

For __bundling__ and __minification__, the size of an application and/or the 
number of HTTP requests made by the browser, especially considering caching, 
are often not as critical as one might think. They are in some contexts, of 
course, but not in _every_ context.

For __transpilation__ of `ES6+` code into `ES5`, I would argue that what cannot
be supported using polyfills should not be used until the relevant browsers are 
eliminated from the list of supported browsers that a project needs to target.

For __compilation__ of `JSX` into `JavaScript`, there are alternatives to `JSX`
that support compilation at run time at the cost of slightly less efficient
template rendering. 

Whether each of these concerns would be better addressed by introducing a build
system, adding a new step to an existing build system or adopting a 
different solution should always be a matter of discussion on a case-by-case
basis. Defaulting to a complete, all-encompassing build system just because 
that is how a given framework is normally used seems myopic at best and 
irresponsible at worst, no matter how good such framework and/or build system
might be. 

All of this said, I personally find `React` to be a delightful framework to 
work with. I appreciate its focus on a more functional approach to UI
management and I appreciate how the community has come together to produce and
share so many reusable components. Whereas I'm not sure that `React` is here to
stay for good - diffing and reconciling are expensive operations, after all - 
it has definitely left an impression that will shape future frameworks.

So, can we use `React` without a build system? Yes, absolutely!

What follows is a simple `React` / `Redux` application that _just works_. No
build system, just pure, client-side JavaScript. Enjoy!

```html
<!doctype html>
<html>
  <head>
  </head>
  <body>
    <div id="app"></div>
    <script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
    <script crossorigin src="https://unpkg.com/redux@4.0.1/dist/redux.js"></script>
    <script crossorigin src="https://unpkg.com/react-redux@5.0.6/dist/react-redux.js"></script>
    <script crossorigin src="https://unpkg.com/htm@2.1.1/dist/htm.js"></script>
    <script>

      /**
       * Bind `htm` to `React.createElement()`
       *
       * Since htm is a generic library, we need to tell it what to "compile" 
       * our templates to. The target should be a function of the form 
       * `h(type, props, ...children)` and can return anything.
       * 
       * @see https://github.com/developit/htm
       */
      const html = htm.bind(React.createElement);

      /*
       * ======================================================================
       *                                 Store
       * ======================================================================
       */

      /**
       * Initial state
       */
      const initialState =  {
        counter: 0,
      };

      /**
       * Reducer
       */
      const rootReducer = (state = initialState, action) => {
        switch (action.type) {
          case 'ADD_ONE':
            return { ...state, counter: state.counter + 1 };
          default:
            return state;
        }
      };

      /**
       * Redux store
       */
      const store = Redux.createStore(rootReducer);

      /**
       * Action creator
       */
      const addOne = () => ({ type: 'ADD_ONE' });

      /*
       * ======================================================================
       *                        State - props connectors
       * ======================================================================
       */
  
      const mapStateToProps = (state) => {
        return { counter: state.counter };
      };
      const mapDispatchToProps = {
        addOne,
      };

      /**
       * Wraps a dumb component and populates the `counter` and  the `addOne` 
       * props with the state's `counter` property and the `addOne` action 
       * creator.
       */
      const connectWithCounter = ReactRedux.connect(
        mapStateToProps, 
        mapDispatchToProps,
      );

      /*
       * ======================================================================
       *                        Store - app connectors
       * ======================================================================
       */

      /**
       * Wraps a component so that all of its children using ReactRedux.connect()
       * can access the store.
       */
      const wrapWithStoreProvider = (Component) => {
        return (props) => {
          return html`
            <${ReactRedux.Provider} store=${store}>
              <${Component} ...${props} />
            <//>
          `;  
        };
      };

      /*
       * ======================================================================
       *                             Components
       * ======================================================================
       */

      const Controls = connectWithCounter((props) => {
        const { counter, addOne } = props; 
        return html`<p>${counter} <button onClick=${addOne}>Add one</button></p>`;
      });

      const App = wrapWithStoreProvider(() => {
        return html`
          <${Controls} />
        `;
      });

      ReactDOM.render(App(), document.getElementById('app'));

    </script>
  </body>
</html>
```
