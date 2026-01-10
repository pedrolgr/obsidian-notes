When we say a variable is global scoped, function scoped or block scoped, it means that this variable just respect this scope limitations:

For example [[Function Scope]]

``` javascript
function foo() {
    {
        var a = 1;
        let b = 2;
        const c = 3;
        
        console.log(a)      // 1
        console.log(b, c)   // 2, 3
    }
    
    console.log(a) // 1
    console.log(b, c) // ERROR
}

foo();

console.log(a) // ERROR
```
In this case, [[var]], [[let]] and [[const]] are declared inside `{}`, they are inside a block scope. When we try to `console.log` then inside the braces we have the value of then, but when we try to do it outside the braces (now we are inside the [[Function Scope]]) just `var a`is printed in the console, because `var`declaration is function scoped, the maximum scope they respect is [[Function Scope]] or [[Global Scope]], so if we try to call `var a`outside the function we will have an error because now, `var a` will respect the [[Function Scope]].

Is the same for the others scopes...