---
title: "React v18.0"
---

29 de marzo de 2022 por [The React Team](/community/team)

---

<Intro>

React 18 ya está disponible en npm! En nuestro último artículo, compartimos instrucciones paso a paso para [actualizar tu aplicación a React 18](/blog/2022/03/08/react-18-upgrade-guide). En este artículo, daremos una descripción general de las novedades en React 18 y lo que significa para el futuro.

</Intro>

---

Nuestra última versión importante incluye mejoras listas para usar, como el procesamiento por lotes automático, nuevas API como startTransition y renderizado en el servidor con transmisión con soporte para Suspense.

Muchas de las características en React 18 se basan en nuestro nuevo renderizador concurrente, un cambio interno que desbloquea nuevas y poderosas capacidades. React Concurrente es opcional, solo se activa cuando se utiliza una característica concurrente, pero creemos que tendrá un gran impacto en la forma en que las personas construyen aplicaciones.

Hemos dedicado años a investigar y desarrollar el soporte para la concurrencia en React, y nos hemos esforzado aún más para ofrecer un camino de adopción gradual para los usuarios existentes. El verano pasado, [formamos el Grupo de Trabajo de React 18](/blog/2021/06/08/the-plan-for-react-18) para recopilar comentarios de expertos en la comunidad y garantizar una experiencia de actualización fluida para todo el ecosistema de React.

En caso de que te lo hayas perdido, compartimos gran parte de esta visión en React Conf 2021.

* En [la presentación principal](https://www.youtube.com/watch?v=FZ0cG47msEk&list=PLNG_1j3cPCaZZ7etkzWA7JfdmKWT0pMsa), explicamos cómo React 18 encaja en nuestra misión de facilitar a los desarrolladores la creación de excelentes experiencias de usuario
* [Shruti Kapoor](https://twitter.com/shrutikapoor08) [demostró cómo utilizar las nuevas características de React 18](https://www.youtube.com/watch?v=ytudH8je5ko&list=PLNG_1j3cPCaZZ7etkzWA7JfdmKWT0pMsa&index=2)
* [Shaundai Person](https://twitter.com/shaundai) nos brindó una descripción general del [renderizado en tiempo real en el servidor con Suspense](https://www.youtube.com/watch?v=pj5N-Khihgc&list=PLNG_1j3cPCaZZ7etkzWA7JfdmKWT0pMsa&index=3)

A continuación se muestra una descripción completa de lo que puedes esperar en esta versión, comenzando con el Renderizado Concurrente.

<Note>

Para los usuarios de React Native, React 18 se lanzará en React Native con la Nueva Arquitectura de React Native. Para más información, puedes ver la [presentación oficial de la React Conf aquí](https://www.youtube.com/watch?v=FZ0cG47msEk&t=1530s).

</Note>

## Que es React Concurrente? {/*what-is-concurrent-react*/}

La adición más importante en React 18 es algo en lo que esperamos que nunca tengas que pensar: la concurrencia. Creemos que esto es en gran medida cierto para los desarrolladores de aplicaciones, aunque la situación puede ser un poco más complicada para los mantenedores de bibliotecas.

La concurrencia no es una característica en sí misma. Es un nuevo mecanismo detrás de escena que permite a React preparar múltiples versiones de tu interfaz de usuario al mismo tiempo. Puedes pensar en la concurrencia como un detalle de implementación: es valiosa debido a las características que desbloquea. React utiliza técnicas sofisticadas en su implementación interna, como colas de prioridad y múltiples búferes. Pero no verás esos conceptos en ninguna de nuestras APIs públicas.

Cuando diseñamos las APIs, tratamos de ocultar los detalles de implementación a los desarrolladores. Como desarrollador de React, te enfocas en cómo quieres que sea la experiencia del usuario y React se encarga de cómo ofrecer esa experiencia. Por lo tanto, no esperamos que los desarrolladores de React sepan cómo funciona la concurrencia bajo el capó.

Sin embargo, React Concurrente es más importante que un simple detalle de implementación, es una actualización fundamental en el modelo de renderizado central de React. Entonces, aunque no es muy importante saber cómo funciona la concurrencia, puede valer la pena saber qué es a grandes rasgos.

Una propiedad clave de React Concurrente es que el renderizado es interrumpible. Cuando actualizas por primera vez a React 18, antes de agregar cualquier característica concurrente, las actualizaciones se renderizan de la misma manera que en versiones anteriores de React: en una transacción única, sin interrupciones y sincrónica. Con el renderizado sincrónico, una vez que comienza el renderizado de una actualización, nada puede interrumpirlo hasta que el usuario pueda ver el resultado en la pantalla.

En un renderizado concurrente, esto no siempre es el caso. React puede comenzar a renderizar una actualización, pausar en el medio y luego continuar más tarde. Incluso puede abandonar por completo un renderizado en progreso. React garantiza que la interfaz de usuario se mantendrá consistente incluso si se interrumpe un renderizado. Para lograr esto, espera para realizar mutaciones en el DOM hasta el final, una vez que se ha evaluado todo el árbol. Con esta capacidad, React puede preparar nuevas pantallas en segundo plano sin bloquear el hilo principal. Esto significa que la interfaz de usuario puede responder de inmediato a la entrada del usuario incluso si se encuentra en medio de una tarea de renderizado grande, creando una experiencia de usuario fluida.

Otro ejemplo es el estado reutilizable. React Concurrente puede eliminar secciones de la interfaz de usuario de la pantalla y luego agregarlas nuevamente más tarde, reutilizando el estado anterior. Por ejemplo, cuando un usuario cambia de pestaña en una pantalla y vuelve, React debería poder restaurar la pantalla anterior en el mismo estado en el que se encontraba antes. En una próxima versión menor, planeamos agregar un nuevo componente llamado `<Offscreen>` que implementa este patrón. Del mismo modo, podrás utilizar Offscreen para preparar una nueva interfaz de usuario en segundo plano para que esté lista antes de que el usuario la revele.

El renderizado concurrente es una poderosa herramienta nueva en React y la mayoría de nuestras nuevas características están diseñadas para aprovecharla, incluyendo Suspense, transiciones y renderizado en tiempo real en el servidor. Pero React 18 es solo el comienzo de lo que buscamos construir sobre esta nueva base.

## Adoptando Gradualmente las Características Concurrentes {/*gradually-adopting-concurrent-features*/}

Técnicamente, el renderizado concurrente es un cambio disruptivo. Debido a que el renderizado concurrente es interrumpible, los componentes se comportan de manera ligeramente diferente cuando está habilitado.

En nuestras pruebas, hemos actualizado miles de componentes a React 18. Lo que hemos encontrado es que casi todos los componentes existentes "simplemente funcionan" con el renderizado concurrente, sin necesidad de realizar cambios. Sin embargo, algunos de ellos pueden requerir un esfuerzo adicional de migración. Aunque los cambios suelen ser pequeños, aún tienes la capacidad de hacerlos a tu propio ritmo. El nuevo comportamiento de renderizado en React 18 **solo se activa en las partes de tu aplicación que utilizan nuevas características.**

La estrategia general de actualización consiste en hacer que tu aplicación funcione con React 18 sin romper el código existente. Luego, puedes comenzar gradualmente a agregar características concurrentes a tu propio ritmo. Puedes utilizar [`<StrictMode>`](/reference/react/StrictMode) para ayudar a detectar errores relacionados con la concurrencia durante el desarrollo. Strict Mode no afecta el comportamiento en producción, pero durante el desarrollo mostrará advertencias adicionales y duplicará la invocación de funciones que se espera que sean idempotentes. No detectará todo, pero es efectivo para prevenir los errores más comunes.

Después de actualizar a React 18, podrás comenzar a utilizar características concurrentes de inmediato. Por ejemplo, puedes utilizar startTransition para navegar entre pantallas sin bloquear la entrada del usuario. O usar useDeferredValue para limitar la frecuencia de las costosas re-renderizaciones.

Sin embargo, a largo plazo, esperamos que la forma principal de agregar concurrencia a tu aplicación sea utilizando una biblioteca o marco de trabajo compatible con la concurrencia. En la mayoría de los casos, no interactuarás directamente con las APIs concurrentes. Por ejemplo, en lugar de que los desarrolladores llamen a startTransition cada vez que naveguen a una nueva pantalla, las bibliotecas de enrutamiento envolverán automáticamente las navegaciones en startTransition.

Puede llevar algún tiempo que las bibliotecas se actualicen para ser compatibles con la concurrencia. Hemos proporcionado nuevas APIs para facilitar a las bibliotecas aprovechar las características concurrentes. Mientras tanto, te pedimos paciencia con los mantenedores mientras trabajamos en la migración gradual del ecosistema de React.

Para obtener más información, consulta nuestra publicación anterior: [Como actualizar a React 18](/blog/2022/03/08/react-18-upgrade-guide).

## Suspense in Data Frameworks {/*suspense-in-data-frameworks*/}

In React 18, you can start using [Suspense](/reference/react/Suspense) for data fetching in opinionated frameworks like Relay, Next.js, Hydrogen, or Remix. Ad hoc data fetching with Suspense is technically possible, but still not recommended as a general strategy.

In the future, we may expose additional primitives that could make it easier to access your data with Suspense, perhaps without the use of an opinionated framework. However, Suspense works best when it’s deeply integrated into your application’s architecture: your router, your data layer, and your server rendering environment. So even long term, we expect that libraries and frameworks will play a crucial role in the React ecosystem.

As in previous versions of React, you can also use Suspense for code splitting on the client with React.lazy. But our vision for Suspense has always been about much more than loading code — the goal is to extend support for Suspense so that eventually, the same declarative Suspense fallback can handle any asynchronous operation (loading code, data, images, etc).

## Server Components is Still in Development {/*server-components-is-still-in-development*/}

[**Server Components**](/blog/2020/12/21/data-fetching-with-react-server-components) is an upcoming feature that allows developers to build apps that span the server and client, combining the rich interactivity of client-side apps with the improved performance of traditional server rendering. Server Components is not inherently coupled to Concurrent React, but it’s designed to work best with concurrent features like Suspense and streaming server rendering.

Server Components is still experimental, but we expect to release an initial version in a minor 18.x release. In the meantime, we’re working with frameworks like Next.js, Hydrogen, and Remix to advance the proposal and get it ready for broad adoption.

## What's New in React 18 {/*whats-new-in-react-18*/}

### New Feature: Automatic Batching {/*new-feature-automatic-batching*/}

Batching is when React groups multiple state updates into a single re-render for better performance. Without automatic batching, we only batched updates inside React event handlers. Updates inside of promises, setTimeout, native event handlers, or any other event were not batched in React by default. With automatic batching, these updates will be batched automatically:


```js
// Before: only React events were batched.
setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // React will render twice, once for each state update (no batching)
}, 1000);

// After: updates inside of timeouts, promises,
// native event handlers or any other event are batched.
setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // React will only re-render once at the end (that's batching!)
}, 1000);
```

For more info, see this post for [Automatic batching for fewer renders in React 18](https://github.com/reactwg/react-18/discussions/21).

### New Feature: Transitions {/*new-feature-transitions*/}

A transition is a new concept in React to distinguish between urgent and non-urgent updates.

* **Urgent updates** reflect direct interaction, like typing, clicking, pressing, and so on.
* **Transition updates** transition the UI from one view to another.

Urgent updates like typing, clicking, or pressing, need immediate response to match our intuitions about how physical objects behave. Otherwise they feel "wrong". However, transitions are different because the user doesn’t expect to see every intermediate value on screen.

For example, when you select a filter in a dropdown, you expect the filter button itself to respond immediately when you click. However, the actual results may transition separately. A small delay would be imperceptible and often expected. And if you change the filter again before the results are done rendering, you only care to see the latest results.

Typically, for the best user experience, a single user input should result in both an urgent update and a non-urgent one. You can use startTransition API inside an input event to inform React which updates are urgent and which are "transitions":


```js
import { startTransition } from 'react';

// Urgent: Show what was typed
setInputValue(input);

// Mark any state updates inside as transitions
startTransition(() => {
  // Transition: Show the results
  setSearchQuery(input);
});
```


Updates wrapped in startTransition are handled as non-urgent and will be interrupted if more urgent updates like clicks or key presses come in. If a transition gets interrupted by the user (for example, by typing multiple characters in a row), React will throw out the stale rendering work that wasn’t finished and render only the latest update.


* `useTransition`: a hook to start transitions, including a value to track the pending state.
* `startTransition`: a method to start transitions when the hook cannot be used.

Transitions will opt in to concurrent rendering, which allows the update to be interrupted. If the content re-suspends, transitions also tell React to continue showing the current content while rendering the transition content in the background (see the [Suspense RFC](https://github.com/reactjs/rfcs/blob/main/text/0213-suspense-in-react-18.md) for more info).

[See docs for transitions here](/reference/react/useTransition).

### New Suspense Features {/*new-suspense-features*/}

Suspense lets you declaratively specify the loading state for a part of the component tree if it's not yet ready to be displayed:

```js
<Suspense fallback={<Spinner />}>
  <Comments />
</Suspense>
```

Suspense makes the "UI loading state" a first-class declarative concept in the React programming model. This lets us build higher-level features on top of it.

We introduced a limited version of Suspense several years ago. However, the only supported use case was code splitting with React.lazy, and it wasn't supported at all when rendering on the server.

In React 18, we've added support for Suspense on the server and expanded its capabilities using concurrent rendering features.

Suspense in React 18 works best when combined with the transition API. If you suspend during a transition, React will prevent already-visible content from being replaced by a fallback. Instead, React will delay the render until enough data has loaded to prevent a bad loading state.

For more, see the RFC for [Suspense in React 18](https://github.com/reactjs/rfcs/blob/main/text/0213-suspense-in-react-18.md).

### New Client and Server Rendering APIs {/*new-client-and-server-rendering-apis*/}

In this release we took the opportunity to redesign the APIs we expose for rendering on the client and server. These changes allow users to continue using the old APIs in React 17 mode while they upgrade to the new APIs in React 18.

#### React DOM Client {/*react-dom-client*/}

These new APIs are now exported from `react-dom/client`:

* `createRoot`: New method to create a root to `render` or `unmount`. Use it instead of `ReactDOM.render`. New features in React 18 don't work without it.
* `hydrateRoot`: New method to hydrate a server rendered application. Use it instead of  `ReactDOM.hydrate` in conjunction with the new React DOM Server APIs. New features in React 18 don't work without it.

Both `createRoot` and `hydrateRoot` accept a new option called `onRecoverableError` in case you want to be notified when React recovers from errors during rendering or hydration for logging. By default, React will use [`reportError`](https://developer.mozilla.org/en-US/docs/Web/API/reportError), or `console.error` in the older browsers.

[See docs for React DOM Client here](/reference/react-dom/client).

#### React DOM Server {/*react-dom-server*/}

These new APIs are now exported from `react-dom/server` and have full support for streaming Suspense on the server:

* `renderToPipeableStream`: for streaming in Node environments.
* `renderToReadableStream`: for modern edge runtime environments, such as Deno and Cloudflare workers.

The existing `renderToString` method keeps working but is discouraged.

[See docs for React DOM Server here](/reference/react-dom/server).

### New Strict Mode Behaviors {/*new-strict-mode-behaviors*/}

In the future, we’d like to add a feature that allows React to add and remove sections of the UI while preserving state. For example, when a user tabs away from a screen and back, React should be able to immediately show the previous screen. To do this, React would unmount and remount trees using the same component state as before.

This feature will give React apps better performance out-of-the-box, but requires components to be resilient to effects being mounted and destroyed multiple times. Most effects will work without any changes, but some effects assume they are only mounted or destroyed once.

To help surface these issues, React 18 introduces a new development-only check to Strict Mode. This new check will automatically unmount and remount every component, whenever a component mounts for the first time, restoring the previous state on the second mount.

Before this change, React would mount the component and create the effects:

```
* React mounts the component.
  * Layout effects are created.
  * Effects are created.
```


With Strict Mode in React 18, React will simulate unmounting and remounting the component in development mode:

```
* React mounts the component.
  * Layout effects are created.
  * Effects are created.
* React simulates unmounting the component.
  * Layout effects are destroyed.
  * Effects are destroyed.
* React simulates mounting the component with the previous state.
  * Layout effects are created.
  * Effects are created.
```

[See docs for ensuring reusable state here](/reference/react/StrictMode#fixing-bugs-found-by-re-running-effects-in-development).

### New Hooks {/*new-hooks*/}

#### useId {/*useid*/}

`useId` is a new hook for generating unique IDs on both the client and server, while avoiding hydration mismatches. It is primarily useful for component libraries integrating with accessibility APIs that require unique IDs. This solves an issue that already exists in React 17 and below, but it's even more important in React 18 because of how the new streaming server renderer delivers HTML out-of-order. [See docs here](/reference/react/useId).

> Note
>
> `useId` is **not** for generating [keys in a list](/learn/rendering-lists#where-to-get-your-key). Keys should be generated from your data.

#### useTransition {/*usetransition*/}

`useTransition` and `startTransition` let you mark some state updates as not urgent. Other state updates are considered urgent by default. React will allow urgent state updates (for example, updating a text input) to interrupt non-urgent state updates (for example, rendering a list of search results). [See docs here](/reference/react/useTransition)

#### useDeferredValue {/*usedeferredvalue*/}

`useDeferredValue` lets you defer re-rendering a non-urgent part of the tree. It is similar to debouncing, but has a few advantages compared to it. There is no fixed time delay, so React will attempt the deferred render right after the first render is reflected on the screen. The deferred render is interruptible and doesn't block user input. [See docs here](/reference/react/useDeferredValue).

#### useSyncExternalStore {/*usesyncexternalstore*/}

`useSyncExternalStore` is a new hook that allows external stores to support concurrent reads by forcing updates to the store to be synchronous. It removes the need for useEffect when implementing subscriptions to external data sources, and is recommended for any library that integrates with state external to React. [See docs here](/reference/react/useSyncExternalStore).

> Note
>
> `useSyncExternalStore` is intended to be used by libraries, not application code.

#### useInsertionEffect {/*useinsertioneffect*/}

`useInsertionEffect` is a new hook that allows CSS-in-JS libraries to address performance issues of injecting styles in render. Unless you’ve already built a CSS-in-JS library we don’t expect you to ever use this. This hook will run after the DOM is mutated, but before layout effects read the new layout. This solves an issue that already exists in React 17 and below, but is even more important in React 18 because React yields to the browser during concurrent rendering, giving it a chance to recalculate layout. [See docs here](/reference/react/useInsertionEffect).

> Note
>
> `useInsertionEffect` is intended to be used by libraries, not application code.

## How to Upgrade {/*how-to-upgrade*/}

See [How to Upgrade to React 18](/blog/2022/03/08/react-18-upgrade-guide) for step-by-step instructions and a full list of breaking and notable changes.

## Changelog {/*changelog*/}

### React {/*react*/}

* Add `useTransition` and `useDeferredValue` to separate urgent updates from transitions. ([#10426](https://github.com/facebook/react/pull/10426), [#10715](https://github.com/facebook/react/pull/10715), [#15593](https://github.com/facebook/react/pull/15593), [#15272](https://github.com/facebook/react/pull/15272), [#15578](https://github.com/facebook/react/pull/15578), [#15769](https://github.com/facebook/react/pull/15769), [#17058](https://github.com/facebook/react/pull/17058), [#18796](https://github.com/facebook/react/pull/18796), [#19121](https://github.com/facebook/react/pull/19121), [#19703](https://github.com/facebook/react/pull/19703), [#19719](https://github.com/facebook/react/pull/19719), [#19724](https://github.com/facebook/react/pull/19724), [#20672](https://github.com/facebook/react/pull/20672), [#20976](https://github.com/facebook/react/pull/20976) by [@acdlite](https://github.com/acdlite), [@lunaruan](https://github.com/lunaruan), [@rickhanlonii](https://github.com/rickhanlonii), and [@sebmarkbage](https://github.com/sebmarkbage))
* Add `useId` for generating unique IDs. ([#17322](https://github.com/facebook/react/pull/17322), [#18576](https://github.com/facebook/react/pull/18576), [#22644](https://github.com/facebook/react/pull/22644), [#22672](https://github.com/facebook/react/pull/22672), [#21260](https://github.com/facebook/react/pull/21260) by [@acdlite](https://github.com/acdlite), [@lunaruan](https://github.com/lunaruan), and [@sebmarkbage](https://github.com/sebmarkbage))
* Add `useSyncExternalStore` to help external store libraries integrate with React. ([#15022](https://github.com/facebook/react/pull/15022), [#18000](https://github.com/facebook/react/pull/18000), [#18771](https://github.com/facebook/react/pull/18771), [#22211](https://github.com/facebook/react/pull/22211), [#22292](https://github.com/facebook/react/pull/22292), [#22239](https://github.com/facebook/react/pull/22239), [#22347](https://github.com/facebook/react/pull/22347), [#23150](https://github.com/facebook/react/pull/23150) by [@acdlite](https://github.com/acdlite), [@bvaughn](https://github.com/bvaughn), and [@drarmstr](https://github.com/drarmstr))
* Add `startTransition` as a version of `useTransition` without pending feedback. ([#19696](https://github.com/facebook/react/pull/19696)  by [@rickhanlonii](https://github.com/rickhanlonii))
* Add `useInsertionEffect` for CSS-in-JS libraries. ([#21913](https://github.com/facebook/react/pull/21913)  by [@rickhanlonii](https://github.com/rickhanlonii))
* Make Suspense remount layout effects when content reappears.  ([#19322](https://github.com/facebook/react/pull/19322), [#19374](https://github.com/facebook/react/pull/19374), [#19523](https://github.com/facebook/react/pull/19523), [#20625](https://github.com/facebook/react/pull/20625), [#21079](https://github.com/facebook/react/pull/21079) by [@acdlite](https://github.com/acdlite), [@bvaughn](https://github.com/bvaughn), and [@lunaruan](https://github.com/lunaruan))
* Make `<StrictMode>` re-run effects to check for restorable state. ([#19523](https://github.com/facebook/react/pull/19523) , [#21418](https://github.com/facebook/react/pull/21418)  by [@bvaughn](https://github.com/bvaughn) and [@lunaruan](https://github.com/lunaruan))
* Assume Symbols are always available. ([#23348](https://github.com/facebook/react/pull/23348)  by [@sebmarkbage](https://github.com/sebmarkbage))
* Remove `object-assign` polyfill. ([#23351](https://github.com/facebook/react/pull/23351)  by [@sebmarkbage](https://github.com/sebmarkbage))
* Remove unsupported `unstable_changedBits` API.  ([#20953](https://github.com/facebook/react/pull/20953)  by [@acdlite](https://github.com/acdlite))
* Allow components to render undefined. ([#21869](https://github.com/facebook/react/pull/21869)  by [@rickhanlonii](https://github.com/rickhanlonii))
* Flush `useEffect` resulting from discrete events like clicks synchronously. ([#21150](https://github.com/facebook/react/pull/21150)  by [@acdlite](https://github.com/acdlite))
* Suspense `fallback={undefined}` now behaves the same as `null` and isn't ignored. ([#21854](https://github.com/facebook/react/pull/21854)  by [@rickhanlonii](https://github.com/rickhanlonii))
* Consider all `lazy()` resolving to the same component equivalent. ([#20357](https://github.com/facebook/react/pull/20357)  by [@sebmarkbage](https://github.com/sebmarkbage))
* Don't patch console during first render. ([#22308](https://github.com/facebook/react/pull/22308)  by [@lunaruan](https://github.com/lunaruan))
* Improve memory usage. ([#21039](https://github.com/facebook/react/pull/21039)  by [@bgirard](https://github.com/bgirard))
* Improve messages if string coercion throws (Temporal.*, Symbol, etc.) ([#22064](https://github.com/facebook/react/pull/22064)  by [@justingrant](https://github.com/justingrant))
* Use `setImmediate` when available over `MessageChannel`. ([#20834](https://github.com/facebook/react/pull/20834)  by [@gaearon](https://github.com/gaearon))
* Fix context failing to propagate inside suspended trees. ([#23095](https://github.com/facebook/react/pull/23095)  by [@gaearon](https://github.com/gaearon))
* Fix `useReducer` observing incorrect props by removing the eager bailout mechanism. ([#22445](https://github.com/facebook/react/pull/22445)  by [@josephsavona](https://github.com/josephsavona))
* Fix `setState` being ignored in Safari when appending iframes. ([#23111](https://github.com/facebook/react/pull/23111)  by [@gaearon](https://github.com/gaearon))
* Fix a crash when rendering `ZonedDateTime` in the tree. ([#20617](https://github.com/facebook/react/pull/20617)  by [@dimaqq](https://github.com/dimaqq))
* Fix a crash when document is set to `null` in tests. ([#22695](https://github.com/facebook/react/pull/22695)  by [@SimenB](https://github.com/SimenB))
* Fix `onLoad` not triggering when concurrent features are on. ([#23316](https://github.com/facebook/react/pull/23316)  by [@gnoff](https://github.com/gnoff))
* Fix a warning when a selector returns `NaN`.  ([#23333](https://github.com/facebook/react/pull/23333)  by [@hachibeeDI](https://github.com/hachibeeDI))
* Fix a crash when document is set to `null` in tests. ([#22695](https://github.com/facebook/react/pull/22695) by [@SimenB](https://github.com/SimenB))
* Fix the generated license header. ([#23004](https://github.com/facebook/react/pull/23004)  by [@vitaliemiron](https://github.com/vitaliemiron))
* Add `package.json` as one of the entry points. ([#22954](https://github.com/facebook/react/pull/22954)  by [@Jack](https://github.com/Jack-Works))
* Allow suspending outside a Suspense boundary. ([#23267](https://github.com/facebook/react/pull/23267)  by [@acdlite](https://github.com/acdlite))
* Log a recoverable error whenever hydration fails. ([#23319](https://github.com/facebook/react/pull/23319)  by [@acdlite](https://github.com/acdlite))

### React DOM {/*react-dom*/}

* Add `createRoot` and `hydrateRoot`. ([#10239](https://github.com/facebook/react/pull/10239), [#11225](https://github.com/facebook/react/pull/11225), [#12117](https://github.com/facebook/react/pull/12117), [#13732](https://github.com/facebook/react/pull/13732), [#15502](https://github.com/facebook/react/pull/15502), [#15532](https://github.com/facebook/react/pull/15532), [#17035](https://github.com/facebook/react/pull/17035), [#17165](https://github.com/facebook/react/pull/17165), [#20669](https://github.com/facebook/react/pull/20669), [#20748](https://github.com/facebook/react/pull/20748), [#20888](https://github.com/facebook/react/pull/20888), [#21072](https://github.com/facebook/react/pull/21072), [#21417](https://github.com/facebook/react/pull/21417), [#21652](https://github.com/facebook/react/pull/21652), [#21687](https://github.com/facebook/react/pull/21687), [#23207](https://github.com/facebook/react/pull/23207), [#23385](https://github.com/facebook/react/pull/23385) by [@acdlite](https://github.com/acdlite), [@bvaughn](https://github.com/bvaughn), [@gaearon](https://github.com/gaearon), [@lunaruan](https://github.com/lunaruan), [@rickhanlonii](https://github.com/rickhanlonii), [@trueadm](https://github.com/trueadm), and [@sebmarkbage](https://github.com/sebmarkbage))
* Add selective hydration. ([#14717](https://github.com/facebook/react/pull/14717), [#14884](https://github.com/facebook/react/pull/14884), [#16725](https://github.com/facebook/react/pull/16725), [#16880](https://github.com/facebook/react/pull/16880), [#17004](https://github.com/facebook/react/pull/17004), [#22416](https://github.com/facebook/react/pull/22416), [#22629](https://github.com/facebook/react/pull/22629), [#22448](https://github.com/facebook/react/pull/22448), [#22856](https://github.com/facebook/react/pull/22856), [#23176](https://github.com/facebook/react/pull/23176) by [@acdlite](https://github.com/acdlite), [@gaearon](https://github.com/gaearon), [@salazarm](https://github.com/salazarm), and [@sebmarkbage](https://github.com/sebmarkbage))
* Add `aria-description` to the list of known ARIA attributes. ([#22142](https://github.com/facebook/react/pull/22142)  by [@mahyareb](https://github.com/mahyareb))
* Add `onResize` event to video elements. ([#21973](https://github.com/facebook/react/pull/21973)  by [@rileyjshaw](https://github.com/rileyjshaw))
* Add `imageSizes` and `imageSrcSet` to known props. ([#22550](https://github.com/facebook/react/pull/22550)  by [@eps1lon](https://github.com/eps1lon))
* Allow non-string `<option>` children if `value` is provided.  ([#21431](https://github.com/facebook/react/pull/21431)  by [@sebmarkbage](https://github.com/sebmarkbage))
* Fix `aspectRatio` style not being applied. ([#21100](https://github.com/facebook/react/pull/21100)  by [@gaearon](https://github.com/gaearon))
* Warn if `renderSubtreeIntoContainer` is called. ([#23355](https://github.com/facebook/react/pull/23355)  by [@acdlite](https://github.com/acdlite))

### React DOM Server {/*react-dom-server-1*/}

* Add the new streaming renderer. ([#14144](https://github.com/facebook/react/pull/14144), [#20970](https://github.com/facebook/react/pull/20970), [#21056](https://github.com/facebook/react/pull/21056), [#21255](https://github.com/facebook/react/pull/21255), [#21200](https://github.com/facebook/react/pull/21200), [#21257](https://github.com/facebook/react/pull/21257), [#21276](https://github.com/facebook/react/pull/21276), [#22443](https://github.com/facebook/react/pull/22443), [#22450](https://github.com/facebook/react/pull/22450), [#23247](https://github.com/facebook/react/pull/23247), [#24025](https://github.com/facebook/react/pull/24025), [#24030](https://github.com/facebook/react/pull/24030) by [@sebmarkbage](https://github.com/sebmarkbage))
* Fix context providers in SSR when handling multiple requests. ([#23171](https://github.com/facebook/react/pull/23171)  by [@frandiox](https://github.com/frandiox))
* Revert to client render on text mismatch. ([#23354](https://github.com/facebook/react/pull/23354)  by [@acdlite](https://github.com/acdlite))
* Deprecate `renderToNodeStream`. ([#23359](https://github.com/facebook/react/pull/23359)  by [@sebmarkbage](https://github.com/sebmarkbage))
* Fix a spurious error log in the new server renderer. ([#24043](https://github.com/facebook/react/pull/24043)  by [@eps1lon](https://github.com/eps1lon))
* Fix a bug in the new server renderer. ([#22617](https://github.com/facebook/react/pull/22617)  by [@shuding](https://github.com/shuding))
* Ignore function and symbol values inside custom elements on the server. ([#21157](https://github.com/facebook/react/pull/21157)  by [@sebmarkbage](https://github.com/sebmarkbage))

### React DOM Test Utils {/*react-dom-test-utils*/}

* Throw when `act` is used in production. ([#21686](https://github.com/facebook/react/pull/21686)  by [@acdlite](https://github.com/acdlite))
* Support disabling spurious act warnings with `global.IS_REACT_ACT_ENVIRONMENT`. ([#22561](https://github.com/facebook/react/pull/22561)  by [@acdlite](https://github.com/acdlite))
* Expand act warning to cover all APIs that might schedule React work. ([#22607](https://github.com/facebook/react/pull/22607)  by [@acdlite](https://github.com/acdlite))
* Make `act` batch updates. ([#21797](https://github.com/facebook/react/pull/21797)  by [@acdlite](https://github.com/acdlite))
* Remove warning for dangling passive effects. ([#22609](https://github.com/facebook/react/pull/22609)  by [@acdlite](https://github.com/acdlite))

### React Refresh {/*react-refresh*/}

* Track late-mounted roots in Fast Refresh. ([#22740](https://github.com/facebook/react/pull/22740)  by [@anc95](https://github.com/anc95))
* Add `exports` field to `package.json`. ([#23087](https://github.com/facebook/react/pull/23087)  by [@otakustay](https://github.com/otakustay))

### Server Components (Experimental) {/*server-components-experimental*/}

* Add Server Context support. ([#23244](https://github.com/facebook/react/pull/23244)  by [@salazarm](https://github.com/salazarm))
* Add `lazy` support. ([#24068](https://github.com/facebook/react/pull/24068)  by [@gnoff](https://github.com/gnoff))
* Update webpack plugin for webpack 5 ([#22739](https://github.com/facebook/react/pull/22739)  by [@michenly](https://github.com/michenly))
* Fix a mistake in the Node loader. ([#22537](https://github.com/facebook/react/pull/22537)  by [@btea](https://github.com/btea))
* Use `globalThis` instead of `window` for edge environments. ([#22777](https://github.com/facebook/react/pull/22777)  by [@huozhi](https://github.com/huozhi))

