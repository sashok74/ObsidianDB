const element = <h1>Привет, мир</h1>;
ReactDOM.render(element, document.getElementById('root'));

единственный способ обновить интерфейс — создать новый элемент и 
передать его в ReactDOM.render().

### Компоненты
1. функциональные, т.к. пишцтся как функция

function Welcome(props) {
  return <h1>Привет, {props.name}</h1>;
}

2.  класс из ES6

class Welcome extends React.Component {
  render() {
    return <h1>Привет, {this.props.name}</h1>;
  }
}

Когда React видит элемент, представляющий пользовательский компонент, 
он передаёт JSX-атрибуты этому компоненту в виде единственного объекта. 
Мы называем этот объект «props».

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);

React вызывает Welcome с объектом {name: 'Sara'} как props.
Welcome возвращает элемент <h1>Hello, Sara</h1>

Примечание: 
Всегда именуйте компоненты с заглавной буквы. а <div /> со строчной.
Свойства объекта props доступны только для чтения.
Все React-компоненты должны вести себя как чистые функции 
в плане своих свойств. Не изменять свои входные данные.

хуками методЫ жизненного цикла: 
componentDidMount() - Конструктор, монтирование - после вставки в DOM.
componentWillUnmount() - Диструктор

### жизненный цикл компонента.
конструктор компонента
Render компонента
обновление DOM
вставляетс вывод компонента в DOM
componentDidMount() - монтирование компонента
компонент удаляется из DOM
componentWillUnmount()

### обновление состояния
при создании компонента.
constructor(props) {
    super(props); ---метод предка
    this.state = {
      posts: [],
      comments: []
    };
  }
самостоятельно их обновлять с помощью отдельных вызовов setState(): 
    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    }); 
  
JSX позволяет встраивать любое выражение в фигурных скобках

формы по своей реализации сохраняют некоторое внутреннее состояние.
  
  
  




