# Deprecated

**WARNING:** This project has moved to [@react-nano/use-event-source](https://github.com/lusito/react-nano)

## react-use-event-source-ts

[![Minified + gzipped size](https://badgen.net/bundlephobia/minzip/react-use-event-source-ts)](https://www.npmjs.com/package/react-use-event-source-ts)
[![NPM version](https://badgen.net/npm/v/react-use-event-source-ts)](https://www.npmjs.com/package/react-use-event-source-ts)
[![License](https://badgen.net/github/license/lusito/react-use-event-source-ts)](https://github.com/lusito/react-use-event-source-ts/blob/master/LICENSE)
[![Stars](https://badgen.net/github/stars/lusito/react-use-event-source-ts)](https://github.com/lusito/react-use-event-source-ts)
[![Watchers](https://badgen.net/github/watchers/lusito/react-use-event-source-ts)](https://github.com/lusito/react-use-event-source-ts)

A lightweight EventSource (server-sent-events) hook for react, written in TypeScript.

#### Why use this hook?

- Very lightweight (see the badges above for the latest size).
- Flexible and dead simple to use.
- Written in TypeScript
- Only has one required peer dependency: React 16.8.0 or higher.
- Liberal license: [zlib/libpng](https://github.com/Lusito/react-use-event-source-ts/blob/master/LICENSE)

**Beware**: This is currently work in progress. The API might change.

### Installation via NPM

```npm i react-use-event-source-ts```

This library is shipped as es2015 modules. To use them in browsers, you'll have to transpile them using webpack or similar, which you probably already do.

### Examples

#### Plain React

```tsx
import { useEventSource, useEventSourceListener } from "react-use-event-source-ts";

function MyComponent() {
    const [messages, setMessages] = useState<Message[]>([]);
    
    const [eventSource, eventSourceStatus] = useEventSource("api/events", true);
    useEventSourceListener(eventSource, ['update'], evt => {
        setMessages([
            ...messages,
            ...JSON.parse(evt.data)
        ]);
    }, [messages]);

    return (
        <div>
            {eventSourceStatus === "open" ? null : <BusyIndicator />}
            {messages.map((msg) => <div>{msg.text}</div>)}
        </div>
    );
}
```

#### With react-redux

```tsx
import { useEventSource, useEventSourceListener } from "react-use-event-source-ts";
import { Action, Store } from "redux";
import { useStore } from "react-redux";

// Create a custom hook to simplify the process:
export function useEventSourceListenerRedux<S, A extends Action>(source: EventSource | null, types: string[], listener: (store: Store<S, A>, e: EventSourceEvent) => void, dependencies: any[] = []) {
    const store = useStore<S, A>();
    useEventSourceListener(source, types, e => listener(store, e), dependencies);
}

function eventHandler(store: Store<State, Action>, e: EventSourceEvent) {
    store.dispatch(addMessages(JSON.parse(e.data)));
}

function MyComponent() {
    const messages = useSelector(getMessages);
    const [eventSource, eventSourceStatus] = useEventSource("api/events", true);
    useEventSourceListenerRedux(eventSource, ['update'], eventHandler);

    return (
        <div>
            {eventSourceStatus === "open" ? null : <BusyIndicator />}
            {messages.map((msg) => <div>{msg.text}</div>)}
        </div>
    );
}
```

### API

```tsx
// Create an EventSource
function useEventSource(
    url: string, // the url to fetch from
    withCredentials?: boolean, // send credentials or not
    ESClass: EventSourceConstructor = EventSource // optionally override the EventSource class (for example with a polyfill)
) => [
    EventSource | null, // the generated EventSource.. on first call, it will be null.
    EventSourceStatus // The status of the connection can be used to display a busy indicator, error indicator, etc.
];

type EventSourceStatus = "init" | "open" | "closed" | "error";

// Add a listener to the EventSource
function useEventSourceListener(
    source: EventSource | null, // The EventSource from the above hook
    types: string[], // the event types to add the listener to
    listener: (e: EventSourceEvent) => void, // a listener callback (use e.type to get the event type)
    dependencies: any[] = [] // if one of the dependencies changes, the listener will be re-added to the event types.
) => void;

```

### Report isssues

Something not working quite as expected? Do you need a feature that has not been implemented yet? Check the [issue tracker](https://github.com/Lusito/react-use-event-source-ts/issues) and add a new one if your problem is not already listed. Please try to provide a detailed description of your problem, including the steps to reproduce it.

### Contribute

Awesome! If you would like to contribute with a new feature or submit a bugfix, fork this repo and send a pull request. Please, make sure all the unit tests are passing before submitting and add new ones in case you introduced new features.

### License

react-use-event-source-ts has been released under the [zlib/libpng](https://github.com/Lusito/react-use-event-source-ts/blob/master/LICENSE) license, meaning you
can use it free of charge, without strings attached in commercial and non-commercial projects. Credits are appreciated but not mandatory.
