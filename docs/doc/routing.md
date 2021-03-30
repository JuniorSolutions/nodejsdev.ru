# Маршрутизация

С помощью **маршрутизации** описываются ответы Node.js приложения на запросы, поступающие с клиентской стороны или другого сервера (в случае, если реализуется API) на конкретный URL.

## Определение маршрутов

Рассмотрим пример структуры простого [REST API](rest-api.md).

_app.js_

```js
const express = require('express'),
		app = express());

const host = '127.0.0.1';
const port = 7000;

app.get('/api/users', (req, res) => {...});
app.post('/api/users', (req, res) => {...});
app.put('/api/users', (req, res) => {...});
app.delete('/api/users', (req, res) => {...});

app.listen(port, host, () => console.log(`Server listens http://${host}:${port}`));
```

Экземпляр фреймворка Express имеет методы, совпадающие по названию с основными методами HTTP для передачи данных (`get`, `post`, `put`, `delete`) и с помощью которых формируется Node.js маршрутизация. Каждый из этих методов принимает два параметра:

- URL, по которому будет осуществляться запрос;
- обрабатывающую запрос функцию (принимает объект запроса и запрос ответа).

Передаваемые данные и информация об отправителе запроса содержится в [объекта запроса](request-object.md), а с помощью [объекта ответа](response-object.md) формируется ответ на запрос.

При описании Node.js маршрутизации в URL допускается использование специальных символов. Так, символ `?`, указывает на необязательность символа, за которым он следует.

```js
// /api/user и /api/users
app.get('/api/users?', (req, res) => {...});
```

Если необходимо указать необязательность группы символов, оберните их в круглые скобки.

```js
// /api/user и /api/userrs
app.get('/api/user(rs)?', (req, res) => {...});
```

Символ `+` означает, что предшествующий ему символ может повторяться в этом месте неограниченное количество раз.

```js
// /api/users, /api/userss, /api/usersss, ...
app.get('/api/users+', (req, res) => {...});
```

Символ `*` означает, что на ее месте может быть абсолютно любая последовательность символов, не ограниченная по длине.

```js
// /api/users, /api/clients, /api/cart, ...
app.get('/api/*', (req, res) => {...});
```

Также Node.js маршрутизация поддерживает при определении URL регулярные выражения.

```js
// /api/users/1, /api/clients/2, ...
app.get(\/api\/.*s\/[0-9]+$, (req, res) => {...});
```

!!! note ""

    При описании Node js маршрутизации важен порядок определения маршрутов. Запрос попадает в первый подходящий обработчик и дальше поиск уже не осуществляется.

## all()

Метод `all()` позволяет определить один обработчик на все типы HTTP запросов по заданному URL.

```js
// GET | POST | PUT | DELETE /api/users
app.all('/api/users', (req, res) => {...});
```

Теперь все отправляемые на маршрут `/api/users` запросы (неважно, get, post или еще какой-нибудь другой) будут попадать в единый обработчик.

## route()

Использование метода `route()` дает возможность сразу определить для одного URL обработчики для разных HTTP методов.

```js
app.route('/api/users');
	.post((req, res) => {...})
	.put((req, res) => {...})
	.delete((req, res) => {...});
```

## express.Router()

С помощью `express.Router()` можно описать Node.js маршрутизацию как систему взаимосвязанных `Middleware` (промежуточных обработчиков).

_app.js_

```js
const routes = require('./routes')

app.use('/api', routes)
```

_routes.js_

```js
const
	express = require("express"),
	router = express.Router();

router.route('/users')
	.get((req, res) => {...})
	.post((req, res) => {...})
	.put((req, res) => {...})
	.delete((req, res) => {...});

module.exports = router;
```

Node.js `express.Router()` позволяет создавать составные модульные маршруты без повторного указания повторяющихся частей URL.

В текущей статье рассказано про определение маршрутов в Node.js приложении, а про саму обработку получаемых запросов подробно можно узнать в статьях [Объект запроса](request-object.md) и [Объект ответа](response-object.md).