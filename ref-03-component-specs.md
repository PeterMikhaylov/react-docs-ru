## Component Specifications

Когда создается класс компонента через `React.createClass()`, вы должны обеспечить объект спецификации, который содержит метод `render` и опционально может содержать другие методы жизненного цикла, описанные здесь.

> Примечания:
>
> Кроме того, можно использовать простые классы JavaScript, как классовы компонентов. Эти классы могут реализовывать одни и те же методы, хотя с небольшими различиями. Подробную информацию об этих различиях можно посмотреть здесь - [ES6 classes](/react/docs/reusable-components.html#es6-classes).

### render

```javascript
ReactElement render()
```

Метод `render()` является обязательным.

при вызове он должен просматривать `this.props` и `this.state` и возвращать единственный дочерний элемент. Этот мочерний элемент может быть или виртуальным представлением нативного DOM-компонента (такие как `<div />` или `React.DOM.div()`) или другие составные компоненты, которые вы для себя определили.

Вы также может возвращать `null` или `false` который будет указывать, что вы ничего не хотите рендерить. React рендерит тег `<noscript>`, чтобы работать с нашим текущим отличающимся алгоритмом. При возврате `null` или `false`, `ReactDOM.findDOMNode(this)` вернет `null`.

Функция `render()` должна быть *чистой*, это означает, что она не изменяет состояние компонент, она возвращает один и тот же результат каждый раз, когда вызывается и она не может читат и писать в DOM или иным образом взаимодействовать с браузером (например использовать `setTimeout`). Если вам нужно взаимодействовать с браузером, используйте `componentDidMount()` или в другой метод жизненного цикла. Чистый `render()` делает серверный рендеринг более практичным и позволяет меньше задумываться при создании компонентов.


### getInitialState

```javascript
object getInitialState()
```

Вызывается один раз перед монтированием компонента. Возвращаемое значение будет использоваться в качестве начального значения `this.state`.


### getDefaultProps

```javascript
object getDefaultProps()
```

Вызывается единожды и кэшируется при создании нового класса. Значения мапятся (mapping) на `this.props` в том случае, если свойства не являются свойствави родительского компонента (для прверки используестя `in`).

Этот метод вызывается перед любым созданием экземпляра и таким образом, нельзя полагаться на `this.props`. Кроме того, имейте в виду, что любые сложные объекты, возвращаемые `getDefaultProps()` будут общими для экземпляров и некопированными.


### propTypes

```javascript
object propTypes
```

Объект `propTypes` позволяет проверять свойства передаваемые к компонентам. Для получения более подробной информации о `propTypes` смотрите [Reusable Components](/react/docs/reusable-components.html).


### mixins

```javascript
array mixins
```

Массив примисей `mixins` позволяет вам распространить поведение среди нескольких компонентов. Для получения более подробной информации о примесях `mixins` смотрите [Reusable Components](/react/docs/reusable-components.html).


### statics

```javascript
object statics
```

Объект `statics` позволяет определить статические методы, которые могут быть вызваны в классе компонента. Например:

```javascript
var MyComponent = React.createClass({
  statics: {
    customMethod: function(foo) {
      return foo === 'bar';
    }
  },
  render: function() {
  }
});

MyComponent.customMethod('bar');  // true
```

Методы, определенные в этом блоке являются статическими, это означает, что вы можете вызвать их, прежде чем создадутся какие-либо экземпляры компонента. Эти методы не имеют доступа к свойствам или состоянию ваших компонентов. Если вы хотите проверить значение свойств в статическом методе, передавайте caller в свойства как аргумент статического метода.


### displayName

```javascript
string displayName
```

Строка `displayName` используется в сообщениях отладчика. JSX создает это значение автоматически. См [JSX in Depth](/react/docs/jsx-in-depth.html#the-transform).


## Lifecycle Methods

Различные методы выполняющиеся в определенных местах жизненного цикла компонента.


### Mounting: componentWillMount

```javascript
void componentWillMount()
```

Вызывается один раз, на клиенте и сервере, непосредственно перед началом рендеринга. Если вы вызовите `setState` внутри этого метода, `render()` будет видеть обновленное состояние и будет выполнен только один раз, несмотря на изменение состояния.


### Mounting: componentDidMount

```javascript
void componentDidMount()
```

Вызывается один раз, только на клиенте (не на сервере), сразу же после рендеринга. На данном этапе жизненного цикла компонент компонент имеет доступ к `ref` дочерних элементов (например, доступ к представлению DOM). Метод `componentDidMount()` на дочерних компонентах вызывается перед вызовом на родительских компонентах.

Если вы хотите интегрироваться с другими JavaScript-фреймворками, установите таймеры используя `setTimeout` или `setInterval`, или отправляйте AJAX запросы в этом методе.


### Updating: componentWillReceiveProps

```javascript
void componentWillReceiveProps(
  object nextProps
)
```
Вызывается, когда компонент получает новые свойства. Этот метод не вызывается для инициализации рендера.

Используйте метод как возможность воздействовать на свойства до вызова `render()` путем обновления состояния с помощью `this.setState()`. Старые свойства могут быть доступны через `this.props`. Вызов `this.setState()` в этой функции не будет вызывать дополнительный рендеринг.


```javascript
componentWillReceiveProps: function(nextProps) {
  this.setState({
    likesIncreasing: nextProps.likeCount > this.props.likeCount
  });
}
```

> Примечания:
>
> Метода, аналогичного `componentWillReceiveState` не существует. Входящая передача свойств может вызвать изменение состояния, но не наоборот. Если вам необходимо выполнить операции в ответ на изменения состояния, используйте `componentWillUpdate`.


### Updating: shouldComponentUpdate

```javascript
boolean shouldComponentUpdate(
  object nextProps, object nextState
)
```

Вызывается перед рендерингом при получени новых свойств или состояния. Этот метод не вызывается для начального рендеринга или когда используется `forceUpdate`.

Используйте это как возможность `return false`, когда вы уверены, что переход на новые свойства и состояние не потребует обновления компонента.


```javascript
shouldComponentUpdate: function(nextProps, nextState) {
  return nextProps.id !== this.props.id;
}
```

Если `shouldComponentUpdate` возвращает false, то `render()` полностью пропускается до следующего изменения состояния. Кроме того не будут вызываться `componentWillUpdate` и `componentDidUpdate`.

По умолчанию, `shouldComponentUpdate` всегда возвращает `true`, чтобы предотвратить неуловимые ошибки, когда изменяется `state`, но если вы осторожны и всегда относитесь к `state` как к чему-то неизменному и читете только из `props` и `state` в `render()`, то вы можете переопределить `shouldComponentUpdate` чтобы он сравнивал старые свойства и состояние с их заменой.

Если производительность упадет, особенно с десятками или сотнями компонентов, используйте `shouldComponentUpdate`, чтобы ускорить ваше приложение.


### Updating: componentWillUpdate

```javascript
void componentWillUpdate(
  object nextProps, object nextState
)
```

Вызывается непосредственно перед рендерингом, когда будут получены новые свойства или состояние. Этот метод не вызывается для начала рендеринга.

Используйте `componentWillUpdate` как подготовку перед обновлением.


> Примечания:
>
> Вы *не можете* использовать `this.setState()` в этом методе. Если вам нужно обновить состояние в ответ на изменение свойства, используйте `componentWillReceiveProps`.


### Updating: componentDidUpdate

```javascript
void componentDidUpdate(
  object prevProps, object prevState
)
```

Вызывается сразу после обновления компонента в DOM. Этот метод не вызывается для начала рендеринга.

Используйте это как возможность работать с DOM, когда компонент уже обновлен.


### Unmounting: componentWillUnmount

```javascript
void componentWillUnmount()
```

Вызывается непосредственно перед тем, как компонент демонтируется из DOM.

Выполняйте любую необходимую очистку в этом методе такую как: отключение таймеров или очистку любых DOM элементов, которые были созданы в `componentDidMount`.