lumen学习笔记
===================

# 路由

## groupAttributes 键值：

- prefix: 路径前缀
- middleware: 中间件
- namespace: 命名空间，结合 action 中的 uses 键使用

## action: string | array | closure
```
string => return ['uses' => $action]
array => return $action
closure => return [$action]
``` 

>    上述所有形式的参数最终都转换为了 array 的形式

## uri 处理：

> 总是去掉末尾的 '/' 符号，uri 为路径


## action 可以设置的键值：

- middleware
- as: 为此路由取别名，存在 nameRouts 数组中，键为别名，值为 uri
- uses: 配合groupAttributes 中的 namespace，namespace\$action['uses']
- group 和 action 中都可以设置 middleware，middleware 之间用 '|' 隔开
- 路由的存储在 routes 中，键为 $method.$uri，值为 ['method' => $method, 'uri' => $uri, 'action' => $action]


# 中间件

分为两种：

- middleware => TerminableMiddleware
- routeMiddleware 

> TerminableMiddleware 继承自 Middleware，**增加了接口 terminate($request, $response)**，注册到 $app->middleware 中，当请求处理完后被调用。

  ```
  $app->middleware([
    'my_terminatemiddleware' => 'App\Http\Middleware\MyTerminateMiddleware',
 ]);
```

# dispatch

## Pipe

Pipe 中的函数： 返回了一个闭包，给原来的闭包有一个传递参数的机会

```
protected function getInitialSlice(Closure $destination)
    {
        return function($passable) use ($destination)
        {
            return call_user_func($destination, $passable);
        };
    }
```

- pipe 中的 passable 是 Request 对象
- send 传递进来的是 Request 对象 => passable
- through 传递进来的是 middleware => pipe
- then 传递进来的是闭包

# Controller

## Middleware in Controller

控制器中设置中间件的选项 [白名单 | 黑名单]

- only
- except

# Request [use Illuminate\Http\Request;]

包含请求的所有信息，控制器的 action 中，前几个参数可以是 Request 参数，后面的为路由参数。
