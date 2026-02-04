Is a function of [[Array.prototype]], that creates a new array with the results of the function provided on every element of the array.

```javascript
const array = [1, 4, 9, 16];

// Pass a function to map

const mapped = array.map((x) => x * 2);

console.log(mapped);

// Expected output: Array [2, 8, 18, 32]
```

✅ Return a new array;
❌ Immutable (don't change the array);