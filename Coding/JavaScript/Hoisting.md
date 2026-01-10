Is when JS moves to the top of the code all variable declarations

To have a better understanding, check [[Global, Function or Block Scoped]], its necessary understand variables declarations and they scope.

Example of `var`:

```javascript
//js will "move" var x to the top like:
//var x;

console.log(x) // undefined
var x = 1;

function foo(){
//js will "move" var x to the top like:
//var y
	console.log(y) // undefined
	{
		var y = 2;
	}
}

```

Example of `let`and `const`:

```javascript
console.log(x, y) // you cannot access x and y before it initialization
let x = 1;	
const y = 2;
```
They was hoisted, but not initialized like `var`

Examples of errors:

```javascript
// console.log(a) >> a is not defined
// var is function-scoped, is hoisted to the top of foo();

function foo() {
    var a;
    console.log(a)
    // console.log(b) >> b is not defined
    // let b is block-scoped, is hoisted to the top of the braces
    // console.log(c) >> the same of the b
    // const c is block-scoped, is hoisted to the top of the braces
    {
        // console.log(b, c) >> cannot access `b` and `c` before initialization
        // js knows that they exist, but they wasnt initialized
        var a = 1;
        let b = 2;
        const c = 3;
        
        console.log(a)      // 1
        console.log(b, c)   // 2, 3

    }
    
    console.log(a) // 1
    // console.log(b, c) >> b and c is not defined
}

foo();
```

Important to read: [[Global, Function or Block Scoped]], [[Global Scope]], [[Function Scope]], [[Block Scope]], [[var]], [[let]], [[const]], [[Variable Scopes]].