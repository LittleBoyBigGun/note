
- 等号赋值， 属于成员变量
- 否则 为写到原型链上
- super 关键字用于访问对象字面量或类的原型（[[Prototype]]）上的属性，或调用父类的构造函数

```ts
class MyClass {

  name = "MyClass"; //属于成员变量

  getName = () => {
  

    return this.name;

  };

  

  getname():string {

    return this.name

  }

}

  
  

class b extends MyClass {

  name = 'b'

  info():string {

    return super.getname()

  }

}

  

const x = new b()

console.log(x.info())
```

会被转意成

```js
({

  607: function () {

    var t,

      n = this && this.__extends || (t = function (n, o) {

        return t = Object.setPrototypeOf || {

          __proto__: []

        }

        instanceof Array && function (t, n) {

          t.__proto__ = n

        } || function (t, n) {

          for (var o in n)

            Object.prototype.hasOwnProperty.call(n, o) && (t[o] = n[o])

        },

        t(n, o)

      }, function (n, o) {

        if ("function" != typeof o && null !== o)

          throw new TypeError("Class extends value " + String(o) + " is not a constructor or null");

        function e() {

          this.constructor = n

        }

        t(n, o),

        n.prototype = null === o ? Object.create(o) : (e.prototype = o.prototype, new e)

      }),

      o = function (t) {

        function o() {

          var n = null !== t && t.apply(this, arguments) || this;

          return n.name = "b",

          n

        }

        return n(o, t),

        o.prototype.info = function () {

          return t.prototype.getname.call(this)

        },

        o

      }(function () {

        function t() {

          var t = this;

          this.name = "MyClass",

          this.getName = function () {

            return t.name

          }

        }

        return t.prototype.getname = function () {

          return this.name

        },

        t.prototype.getage = function () {

          return this.age

        },

        t

      }()),

      e = new o;

    console.log(e.info())

  }

})[607]();
```