# Anotações sobre hooks do react

## Hooks e recursos abordados

|Nome| Função|
|----|----------|
|[useState](##useState)|Declaração de variáveis capazes de re-renderizar|
|useReducer|Manipula o estado através de uma função que criamos|
|useEffect|Usado para controlar quantas vezes algo acontece|
|useContext|Permite passar informações de um componente para outros por contexto|
|useRef|Usado para armazenar o valor de uma variável sem provocar re-renderização e para acessar elementos da DOM|
|useCallback|Usado para memorizar funções, bom para performance|
|useMemo|Pode ser usado para garantir a referência de um objeto|
|useLayoutEffect|Usado como useEffect porém executa antes de renderizar a página|
|useImperativeHandle|Passar funções do componente filho para o componente pai|
|Custom Hooks|Funções que nós mesmos criamos e queremos reutilizar|
|useDebugValue|Cria um log e ajuda a depurar o código, necessário instalar react dev tools no navegador|
|React Dev Tools|Ferramenta para visualizar os hooks no navegador|

## useState

- Permite gerenciar valores, podendo fazer consulta ou alterações de valor;
- Ele permite re-rendenrizar um componente, diferente de manipulação com variáveis;

**Declarando um useState**

```const [name, setName] = useState("");```

**Atrelando useState e inputs**

- Ajuda na criação de formulários passando os valores do input para o useState através do evento onChange;
- Nesse caso a cada mudança no input o evento onChange altera o valor do useState re-renderizando em tempo real;

**Controlando useState dentro do input**

```<input type="text" value={name} onChange={(e) => setNome(e.target.value)} />```

## useReducer

- Tem a mesma função que o useState de gerenciar valores, porém podemos **executar uma função** na hora de alterar o valor;
- O useReducer recebe um **valor** para gerenciar e uma **função** para alterar este valor;

**Declarando um useReducer**

```
const [number, dispatch] = useReducer((state, action) => {
        // - number é o nome do state
        // - dispatch é o nome de onde executaremos a função para alterar o valor de number
        // - useReducer espera os argumentos state e action
        // - state é a referencia para o valor de number
        // - action é uma ação que pode ser usada ou não
})
```
**Executando o useReducer**

- Para executar esse useReducer devemos usar a função **dispatch**

```<button onClick={dispatch}>Chamar função</button>```

### useReducer com action

- Podemos utilizar o useReducer com **operações mais complexas** e para isso usamos a estrutura **switch com actions**;

```
    const initialTasks = [
        { id: 1, text: "Fazer alguma coisa" },
        { id: 2, text: "Fazer outra coisa" },
    ];

    //Cria o useReducer sem o state declarado e adiciona os cases
    const taskReducer = (state, action) => {
        switch (action.type) {
            case "ADD":
            //Executa algo se o action.type for ADD
            return;

            case "REMOVE":
            //Executa algo se o action.type for REMOVE
            return;
            default:
            return state;
        }
    };
    //Cria um useState para ser manipulado pela função dispatch
    const [taskText, setTaskText] = useState("");
    //Cria o useReducer passando o reducer switch e as variaveis que manipula
    const [tasks, dispatchTask] = useReducer(taskReducer, initialTasks);

    //Executa o metodo dispatch com o type ="ADD"
    const handleSubmit = (e) => {
        e.preventDefault();
        dispatchTask({ type: "ADD" });
    };
    //Executa o metodo dispatch com o type ="REMOVE"
    const removeTask = (id) => {
        dispatchTask({ type: "REMOVE", id: id });
    };
```

## useEffect

- Junto do useState é um dos hooks mais utilizados;
- Permite realizar alterações na **DOM** e requisições **HTTP**;
- O objetivo ao usar useEffect é **Controlar quantas vezes algo acontece**;
- É formado por uma **função** a ser executada e um **array** de dependências;

### useEffect sem array de dependências

- Sempre que renderizar o componente que este useEffect está ele executará;

**Declarando o useEffect sem dependências** 

```
    useEffect(() => {
        console.log("Estou sendo executado!");
    });
```

### useEffect com array vazio

- Ao deixar o array de dependências do useEffect vazio o useEffect executara apenas uma vez ao renderizar;

**Declarando o useEffect com dependência vazia**

```
    useEffect(() => {
        console.log("Estou sendo executado!");
    },[]);//Array de dependências vazio
```

### useEffect com array de dependências

- Neste caso o useEffect fica monitorando um item e se caso este item for alterado o useEffect é executado;
- Isso da um maior controle de quando a função deve ser executada;

**Declarando o useEffect com array de dependência**

```
    cosnt [number, setNumber] = useState(0);
    useEffect(() => {
        console.log(number);
    },[number]); //useEffect monitora number e quando ele for alterado ele vai executar
```

### CleanUp do useEffect

- Alguns efeitos precisam ter uma técnica de cleanup para garantir o funcionamento;
- Não fazer isso pode gerar um comportamento indesejado;
- Exemplo: um timeout que ao mudar de página pode continuar a ser executado pela falta de limpeza;

**Declarando um CleanUp no useEffect**

```
    cosnt [number, setNumber] = useState(0);
    useEffect(() => {
        //função para setar um timer de 2seg antes de executar
        const timer = setTimeout(() => {
            console.log("Hello World");
        }, 2000);
        //Pausa a execução chamando a função que para o timeout
        return () => clearTimeout(timer);
    },[number]); //useEffect monitora number e quando ele for alterado ele vai executar
```

## useContext

- É o hook utilizado para consumir um contexto, da Context API;
- É preciso criar o **contexto** e também o **Provider** e **envolver os componentes** que receberão os valores compartilhados;

**Declarando o hook nameContext**

1. Para começar devemos criar o arquivo nameContext.js dentro da pasta de hooks com o seguinte código:

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

2. No arquivo App.js devemos importar nosso nameContext;

```import nameContext from './context/nameContext';```

3. Encapsulamos todo o aplicativo dentro do context (se formos usar esse context em todo o app);

```
    <div className="App">
      //Encapsulando todos os componentes do aplicativo
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

4. Nas páginas que esse contexto for necessário, fazer as importações e chamada;

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

- Pode ser usado como **useState** para gerenciar valores;
- A diferença é que ele é um **objeto**, seu valor está na propriedade **current**;
- Outra particularidade do useRef é que ele **não re-renderiza o componente** ao ser alterado;
- o useRef pode ser colocado em um useEffect sem dependência e não causa looping infinito;

**Declarando o hook useRef**

```
    const numberRef = useRef(0);
    
    useEffect (() => {
        //incrementa o valor de numberRef a cada renderização do componente atual
        //se fosse useState esse useEffect causaria uma renderização por conta própria
        numberRef.current = numberRef.current + 1;
    });
```

### useRef e o DOM

- Podemos utilizar o useRef para selecionar elementos do **JSX**;
- Isso permite fazer **manipulações de DOM** ou aplicar **funções** como a focus;

**Declarando o hook useRef para manipular o DOM**

```
    //Declaração do useRef
    const inputRef = useRef();
    const [text, setText] = useState("");

    //Função chamada ao enviar o formulário
    const handleSubmit = (e) => {
        e.preventDefault();

        setText("");
        //Após enviar o form e limpar o input ele vai focar no input novamente, isso acelera a digitação
        //Existem varias outras formas de interagir com o input
        inputRef.current.focus();
    };

    return(
        <form onSubmit={handleSubmit}>
            //Input que o useRef monitora atravéz do ref={inputRef}
            <input type="text" ref={inputRef} value={text} onChange={(e) => setText(e.target.value)} />
            <input type="submit" value="Enviar" />
      </form>
    )
```

## useCallback

- Basicamente **memoriza uma função**, fazendo ela **não ser reconstruída** a cada renderização de um componente;
- Existem dois casos para utilizarmos esse hook:
    - O primeiro caso é quando prezamos pela **performance**, não queremos que uma função seja reconstruída toda vez;
    - O segundo caso é quando o próprio React indica que uma função **deveria estar contida** em um useCallback para evitar
        problemas de performance;

**Declarando um useCallback**

- > Esse hook envolve várias págias e é mais fácil acompanhar olhando o código de fato
[useCalback](src/components/HookUseCallback.js)

## useMemo

- Este hook pode ser usado para garantir a **referência de um objeto**;
- Permite que algo seja atrelado a uma referência e não a um valor, isso permite **condicionarmos o useEffect a uma variável**
    de maneira mais inteligente;

**Declarando um hook useMemo**

```
    const premiumNumbers = useMemo(() => {
        //Se esse valor fosse gigante, sem o useMemo ele buscaria a cada render 
        //Com o Memo ele traz uma vez e memoriza e so busca se a pagina re-renderizar
        return ["0", "100", "200"];
    }, []);
```

## useLayoutEffect

- Parecido com o useEffect, a diferença é que esse hook roda **antes de renderizar o componente**;
- Ou seja, é um hook **síncrono**, bloqueando o carregamento da página para o sucesso da sua funcionalidade;
- A ideia é executar algo antes que o usuário veja a página;

**Declarando hooks useLayoutEffect**

```
    useLayoutEffect(() => {
        //O código aqui vai ser executado antes de renderizar o componente
    },[]);
```

## useImperativeHandle

- Temos como acionar ações em um outro componente de forma imperativa;
- Como não podemos passar refs como props, precisamos usar uma função **fowardRef**;
- Isso permite passar as referências e torna esse exemplo viável;

**Declarando hooks useImperativeHandle**

- > Esse hook envolve várias págias e é mais fácil acompanhar olhando o código de fato
[useImperativeHandle](src/components/HookUseImperativeHandle.js)

## Custom hooks

- São os hooks que nós criamos, muitas vezes para **abstrair funções complexas do componente** ou **reaproveitar o código**;

**Declarando hooks custom**

1. Primeiro criar o hook custom dentro da pasta hooks usePrevious.js

```
    export const usePrevious = (value) => {
        //podemos criar qualquer código aqui para ser usado depois
        const ref = useRef

        useEffect(() => {
            ref.current = value;
        })

        return ref.current;
    }
```

2. Na página que usaremos este hook

```
    //import
    import {usePrevious} from '../hooks/usePrevious';

    const [number, setNumber] = useState(0);
    const previous = usePrevious (number);
```

## useDebugValue

- É um hook utilizado para debug;
- Aconselhado para usar em **custom hooks**;
- Adiciona uma área no **React Dev Tools** (Ferramenta instalada no próximo capítulo), ela estará no componente em que o
    hook é utilizado;

**Declarando o hook**

```
    //Importar no custom hook
    import {useDebugValue} from "react";

    //Dentro do export do custom hook
    useDebugValue("Imprimir o que precisar imprimir");
```

## React Dev Tools

- É uma extensão para o navegador chrome e nela conseguimos **entender o que o react está gerando** por
    meio do nosso código;
- Conseguimos também verificar os states e outros parâmetros;

**Fazendo a instalação**

1. buscar no google react dev tools
2. instalar a extensão do chrome
3. Colocar a extensão na página principal
4. No F12 abrir components e é possível ver os hooks