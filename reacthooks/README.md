# Notations about React Hooks

## Approached hooks and functions

|Nome| Função|
|----|----------|
|[useState](#useState)|Declare variables able to re-render|
|[useReducer](#useReducer)|Manipulate the state through a function that we have created|
|[useEffect](#useeffect)|Used to control how many times something happens|
|[useContext](#useContext)|Allows you to pass info from a component to others by context|
|[useRef](#useRef)|Used to store a variable value without re-render and to access DOM elements|
|[useCallback](#useCallback)|Used to memorize functions. Good to increase performance|
|[useMemo](#useMemo)|Can be used to ensure the object reference|
|[useLayoutEffect](#useLayoutEffect)|Used like useEffect. However, it render before the page shows up|
|[useImperativeHandle](#useImperativeHandle)|Pass functions from children component to father component|
|[Custom Hooks](#CustomHooks)|Functions created by ourselves|
|[useDebugValue](#useDebugValue)|Create a log to help debugging codes. React Dev Tools needs to be installed|
|[React Dev Tools](#ReactDevTools)|Tool to visualize hooks in browser|

## useState

- Allows managing states (similar to variables). Being able to make queries or value changes;
- Allows to re-render a component. Which is different to variable manipulation;

**Declaring a useState**

```const [name, setName] = useState("");```

**Linking useState to inputs**

- It helps to create forms. Transferring values from input to useState through event onChange;
- Each input change the event onChange changes the value from useState re-rendering in real time;

**Controlling useState inside the input**

```<input type="text" value={name} onChange={(e) => setNome(e.target.value)} />```

## useReducer

- It has the same function as useState to manage values, but we can **run a function** when changing the value;
- The useReducer gets a **value** to manage and a **function** to change it;

**Declaring a useReducer**

```
const [number, dispatch] = useReducer((state, action) => {
        // - number is the state name
        // - dispatch is the name where we will execute the function to change the value of number
        // - useReducer waits for the arguments state and action
        // - state is the reference to the value of number
        // - action is an action that can be used or not
})
```
**Running theuseReducer**

- To execute this useReducer we must use the function **dispatch**

```<button onClick={dispatch}>Call function</button>```

### useReducer with action

- We can use the useReducer with **more complex operations** and for this we use the **switch with actions** structure;

```
    const initialTasks = [
        { id: 1, text: "Do something" },
        { id: 2, text: "Do other thing" },
    ];

    //Create the useReducer without the declared state and add the cases
    const taskReducer = (state, action) => {
        switch (action.type) {
            case "ADD":
            //Execute something if action.type for ADD
            return;

            case "REMOVE":
            //Execute something if action.type for REMOVE
            return;
            default:
            return state;
        }
    };
    //Creates a useState to be manipulated by the dispatch
    const [taskText, setTaskText] = useState("");
    //Create the useReducer passing the reducer switch and the variables it manipulates
    const [tasks, dispatchTask] = useReducer(taskReducer, initialTasks);

    //Run the dispatch method with the type ="ADD"
    const handleSubmit = (e) => {
        e.preventDefault();
        dispatchTask({ type: "ADD" });
    };
    //Run the dispatch method with the type ="REMOVE"
    const removeTask = (id) => {
        dispatchTask({ type: "REMOVE", id: id });
    };
```

## useEffect

- Along with useState it is one of the most used hooks;
- It allows you to make changes to the **DOM** and **HTTP** requests;
- The purpose of using useEffect is to **Control how many times something happens**;
- It consists of a **function** to be executed and an **array** of dependencies;

### useEffect without dependency array

- Whenever you render the component that this useEffect is on it will execute;

**Declaring the useEffect without dependencies**.

```
    useEffect(() => {
        console.log("I'm running!");
    });
```

### useEffect with empty array

- By leaving the dependency array of the useEffect empty the useEffect will execute only once when rendering;

**Declaring the useEffect with an empty dependency**

```
    useEffect(() => {
        console.log("I'm running!");
    },[]);//Empty dependency array
```

### useEffect with dependency array

- In this case, the useEffect monitors one item and if this item changes, the useEffect is executed;
- This gives more control on when the function should be executed;

## Declaring the useEffect with dependency array

```
    cosnt [number, setNumber] = useState(0);
    useEffect(() => {
        console.log(number);
    },[number]); //useEffect monitors number and when it changes it will execute
```

### Cleanup of useEffect

- Some effects need to have a cleanup technique to ensure that they work;
- Not doing this can generate unwanted behavior;
- Example: a timeout that when changing pages can continue to run due to the lack of cleanup;

**Declaring a CleanUp in the useEffect**

```
    cosnt [number, setNumber] = useState(0);
    useEffect(() => {
        //function to set a 2sec timer before running
        const timer = setTimeout(() => {
            console.log("Hello World");
        }, 2000);
        //Pause execution by calling the function that stops the timeout
        return () => clearTimeout(timer);
    },[number]); //useEffect monitors number and when it changes it will execute
```

## useContext

- It is the hook used to consume a context, from the Context API;
- You need to create the **context** and also the **Provider** and **involve the components** that will receive the shared values;

**Declaring the nameContext hook**

1. To begin we must create the file nameContext.js inside the hooks folder with the following code:

```
    import { createContext } from "react";

    export const SomeContext = createContext();

    export const HooknameContext = ({ children }) => {
        const contextValue = "testing context";

        return (
            <SomeContext.Provider value={{ contextValue }}>
            {children}
            </SomeContext.Provider>
        );
    };

```

2. In the App.js file we must import our nameContext;

```import nameContext from './context/nameContext';```

3. We encapsulate the whole application inside the context (if we are going to use this context throughout the app);

```
    <div className="App">
      //Encapsulating all components of the application
      <nameContext>
        <h1>React Hooks</h1>
        <BrowserRouter>
          <Navbar/>
          <Routes>
            <Route/>
            <Route/>
          </Routes>
        </BrowserRouter>
      </nameContext>
    </div>
```

4. On pages where this context is needed, make the imports and call;

```
    //imports
    import {useContext} from react;
    import { someContext } from './context/nameContext';

    const {contextValue} = useContext(SomeContext);

    return(
        <div>
            <p>Context value: {contextValue}</p>
        </div>
    )
```

## useRef

- It can be used as a **useState** to manage values;
- The difference is that it is an **object**, its value is in the **current** property;
- Another particularity of useRef is that it **doesn't re-render the component** when changed;
- useRef can be placed in a useEffect without dependency and does not cause infinite looping;

**Declaring the useRef hook**

```
    const numberRef = useRef(0);
    
    useEffect (() => {
        //increments the value of numberRef with each rendering of the current component
        //if it were useState this useEffect would cause a rendering on its own
        numberRef.current = numberRef.current + 1;
    });
```

### useRef and DOM

- We can use useRef to select elements from **JSX**;
- This allows us to do **DOM manipulations** or apply **functions** like focus;

**Declaring the useRef hook to manipulate the DOM**

```
    //useRef Declaration
    const inputRef = useRef();
    const [text, setText] = useState("");

    //Function called when sending the form
    const handleSubmit = (e) => {
        e.preventDefault();

        setText("");
        //After sending the form and clearing the input it will focus on the input again, this speeds up typing
        //There are many other ways to interact with the input
        inputRef.current.focus();
    };

    return(
        <form onSubmit={handleSubmit}>
            //Input that useRef monitors through ref={inputRef}
            <input type="text" ref={inputRef} value={text} onChange={(e) => setText(e.target.value)} />
            <input type="submit" value="Enviar" />
      </form>
    )
```

## useCallback

- It basically **memorizes a function**, making it **not get rebuilt** each time a component is rendered;
- There are two cases for using this hook:
    - The first case is when we want more **performance**, we don't want a function to be rebuilt every time;
    - The second case is when React itself indicates that a function **should be contained** in a useCallback to avoid
        performance problems;

**Declaring a useCallback**

- This hook involves several pages and is easiest to follow by looking at the actual code
[useCalback](src/components/HookUseCallback.js)

## useMemo

- This hook can be used to secure the **reference of an object**;
- Allows something to be tied to a reference rather than a value, this allows us to **condition the useEffect to a variable** in a smarter way
    in a smarter way;

**Declaring a useMemo hook**

```
    const premiumNumbers = useMemo(() => {
        // If this value was huge, without useMemo it would fetch every render 
        //With Memo it fetches once and memorizes and only fetches if the page re-renders
        return ["0", "100", "200"];
    }, []);
```

## useLayoutEffect

- Similar to useEffect, the difference is that this hook runs **before rendering the component**;
- In other words, it is a **synchronous** hook, blocking the page from loading for its functionality to succeed;
- The idea is to execute something before the user sees the page;

**Declaring useLayoutEffect hooks**

```
    useLayoutEffect(() => {
        //Code here will be executed before rendering the component
    },[]);
```

## useImperativeHandle

- We have how to trigger actions on another component imperatively;
- Since we can't pass refs as props, we need to use a **fowardRef** function;
- This allows us to pass references and makes this example workable;

**Declaring hooks useImperativeHandle**

- > This hook involves several pages and is easiest to follow by looking at the actual code
[useImperativeHandle](src/components/HookUseImperativeHandle.js)

## CustomHooks

- These are the hooks that we create, often to **abstract complex functions of the component** or **reuse code**;

**Declaring custom hooks**

1. First create the custom hook inside the hooks folder usePrevious.js
```
    export const usePrevious = (value) => {
        //we can create any code here to be used later
        const ref = useRef

        useEffect(() => {
            ref.current = value;
        })

        return ref.current;
    }
```

2. On the page we will use this hook

```
    //import
    import {usePrevious} from '../hooks/usePrevious';

    const [number, setNumber] = useState(0);
    const previous = usePrevious (number);
```

## useDebugValue

- It is a hook used for debugging;
- Recommended for use in **custom hooks**;
- Adds an area in the **React Dev Tools** (Tool installed in the next chapter), it will be in the component in which the
    hook is used;

**Declaring the hook**

```
    //Import into custom hook
    import {useDebugValue} from "react";

    // Inside the custom hook export
    useDebugValue("Imprimir o que precisar imprimir");
```

## ReactDevTools

- It's an extension for the chrome browser and in it we can **understand what react is generating** through our code
    through our code;
- We can also check the states and other parameters;

**Making the installation**

1. search on google react dev tools
2. install the chrome extension
3. Place the extension on the main page
4. At F12 open components and you can see the hooks