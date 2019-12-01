---
title: Why React?
date: 2019-12-01 13:40:56
tags:
---

A new pedigree of Frontend Development.

The following are the preferred technical choices for any new projects that do not have a "**hammer whats already there**" constraint that have been chosen for consistency.

Programming Language: TypeScript 
--------------------------------

Alternatives to avoid:

- **Raw JS** / [**Babel**](https://babeljs.io/): Types can prevent a significant amount of bugs and provide a great means of documenting code that can be validated by a compiler. Therefore we will not dabble in untyped programming languages for Frontend development.
- Other TypeSafe alternatives:
  * [**Dart**](https://dart.dev/) / [**Elm**](https://elm-lang.org/) / [**Blazor**](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) - typed compile to JavaScript languages: Browsers operate on JavaScript using a language that is too far removed from JS means people will need to learn two language and fix bugs at two levels.
  * [**Flow**](https://flow.org/) - A facebook alternative to TypeScript. Flow was a decent experiment into JavaScript type safety by the Facebook team, but news from inside Facebook (chats at conferences) is that it is not actively getting maintained. Therefore it should be avoided.

UI Library: React
-----------------

Alternatives to avoid:

- [**Angular**](https://angular.io/): **Angular** goes through significant breaking changes as can be [witnessed by the changelog](https://github.com/angular/angular/blob/master/CHANGELOG.md). Also **Angular** is a full framework (as opposed to a library), but people rarely use it as a framework as there will be inevitable external dependencies for alternative (even better) community tools e.g. [**rxjs**](https://rxjs.dev/) for state, [**axios**](https://github.com/axios/axios) for api etc.
- [**Vue**](https://vuejs.org/): **Vue** is the third most popular choice. However it not backed by a major enterprise (Facebook for **React**, Google for **Angular**)
- [**Svelte**](https://svelte.dev/): Same reasons as **Vue**.

State Management: [MobX](https://mobx.js.org/)
----------------------------------------------

Alternatives to avoid:

- [**Redux**](https://redux.js.org/): **Redux** is definitely the more popular choice in the React community. However most people will use it with some additional middleware library e.g. [redux-thunk](https://github.com/reduxjs/redux-thunk) or [redux-saga](https://github.com/redux-saga/redux-saga/) leading to multiple ways of doing Frontend. Also, **Redux** is significantly more verbose (because of always new object creation) and difficult to optimise (because of no internal knowledge of what changed).

Styles: CSS in JS
-----------------

Prefer [Emotion](https://github.com/emotion-js/emotion) or [TypeStyle](https://github.com/typestyle/typestyle) over others.

Alternatives to avoid:

- CSS preprocessor (e.g. [**sass**](https://sass-lang.com/) / [**css-modules**](https://github.com/css-modules/css-modules)). For CSS in JS there is an [excellent presentation](https://speakerdeck.com/vjeux/react-css-in-js) that you should checkout. Key reasons are clear code co-location and management practices for CSS, the same way **React** does it for Html (with JSX).

End to End Testing : Cypress
----------------------------

[**Cypress**](https://www.cypress.io/) for E2E testing.

Alternatives to avoid:

- [**Selenium**](https://selenium.dev/) based solutions (e.g. [**Protractor**](https://www.protractortest.org/#/) / [**Nightwatch**](https://nightwatchjs.org/)). **Selenium** operates over a wire transfer protocol that makes it impossible to provide many of the debugging features provided by **Cypress**. **Cypress** operates in process with the tests making automatic retries with greater flake resistance and a significantly improved debugging experience.

HTTP Requests
-------------

Prefer [**axios**](https://github.com/axios/axios) due to is built in TypeScript definitions, great browser support and community guidance.

Alternatives to avoid:

- [**fetch**](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API): Needs polyfill and needs explicit calls for json de-serialization.
