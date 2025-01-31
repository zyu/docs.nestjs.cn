# 自定义路由参数装饰器

Nest 是基于**装饰器**这种语言特性而创建的。它是许多常用编程语言中众所周知的概念，但在 JavaScript 世界中，这个概念仍然相对较新。所以为了更好地理解装饰器是如何工作的，你应该看看 [这篇](https://medium.com/google-developers/exploring-es7-decorators-76ecb65fb841) 文章。下面给出一个简单的定义：

ES2016 的装饰器是一个可以将目标对象，名称和属性描述符作为参数的返回函数的表达式。你可以通过装饰器前缀 `@` 来使用它，并且把它放在你试图装饰的顶部。装饰器可以被定义为一个类或是属性。

## 参数装饰器

Nest 提供了一组有用的参数装饰器，可以和 HTTP 路由处理器（route handlers）一起使用。下面是一组装饰器和普通表达式对象的对照。

|                                           |                                              |
| ----------------------------------------- | -------------------------------------------- |
| @Request()                                | req                                          |
| @Response()                               | res                                          |
| @Next()                                   | next                                         |
| @Session()                                | req.session                                  |
| @Param(param?: string)                     | req.params / req.params[param]               |
| @Body(param?: string)                     | req.body / req.body[param]                   |
| @Query(param?: string)                   | req.query / req.query[param]                 |
| @Headers(param?: string)　　　　　　　   　　| req.headers / req.headers[param]　　　　　　　 |

另外，你还可以创建你自己的**自定义装饰器**。为什么它很有用呢？

在 node.js 的世界中，把属性值附加到 **request** 对象中是一种很常见的做法。然后你可以在任何时候在路由处理程器（route handlers）中手动取到它们，例如，使用下面这个构造：

```typescript
const user = req.user;
```

为了使其更具可读性和透明性，我们可以创建 `@User()` 装饰器并且在所有控制器中重复利用它。

> user.decorator.ts

```typescript
import { createParamDecorator } from '@nestjs/common';

export const User = createParamDecorator((data, req) => {
  return req.user;
});
```

现在你可以在任何你想要的地方很方便地使用它。

```typescript
@Get()
async findOne(@User() user: UserEntity) {
  console.log(user);
}
```

## 传递数据（Passing data）

当你的装饰器的行为依赖于某些条件时，你可以使用 `data` 参数给装饰器的工厂函数传参。例如，下面的构造：

```typescript
@Get()
async findOne(@User('test') user: UserEntity) {
  console.log(user);
}
```

可以通过 `data` 访问传进来的 `test` 字符串：

> user.decorator.ts

```typescript
import { createParamDecorator } from '@nestjs/common';

export const User = createParamDecorator((data, req) => {
  console.log(data); // test
  return req.user;
});
```

## 使用管道

Nest 对待自定义的路由参数装饰器和这些内置的装饰器（@Body()，@Param() 和 @Query()）一样。这意味着管道也会因为自定义注释参数（在本例中为 `user` 参数）而被执行。此外，你还可以直接将管道应用到自定义装饰器上： 

```typescript
@Get()
async findOne(@User(new ValidationPipe()) user: UserEntity) {
  console.log(user);
}
```

 ### 译者署名

| 用户名 | 头像 | 职能 | 签名 |
|---|---|---|---|
| [@zuohuadong](https://github.com/zuohuadong)  | <img class="avatar-66 rm-style" src="https://wx3.sinaimg.cn/large/006fVPCvly1fmpnlt8sefj302d02s742.jpg">  |  翻译  | 专注于 caddy 和 nest，[@zuohuadong](https://github.com/zuohuadong/) at Github  |
| [@Drixn](https://drixn.com/)  | <img class="avatar-66 rm-style" src="https://cdn.drixn.com/img/src/avatar1.png">  |  翻译  | 专注于 nginx 和 C++，[@Drixn](https://drixn.com/) |
| [@franken133](https://github.com/franken133)  | <img class="avatar rounded-2" src="https://avatars0.githubusercontent.com/u/17498284?s=400&amp;u=aa9742236b57cbf62add804dc3315caeede888e1&amp;v=4" height="70">  |  翻译  | 专注于 java 和 nest，[@franken133](https://github.com/franken133)|
