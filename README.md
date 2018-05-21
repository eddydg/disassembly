# What is this


# Create your C/C++ app

fibo.c
```
#include <emscripten.h>

EMSCRIPTEN_KEEPALIVE
int fib(int n) {
int i, t, a = 0, b = 1;
  for (i = 0; i < n; i++) {
    t = a + b;
    a = b;
    b = t;
  }
  return b;
}
```


# Compile C/C++ files to JS modules

https://developer.mozilla.org/fr/docs/WebAssembly/C_to_wasm
```bash
git clone https://github.com/juj/emsdk.git
cd emsdk

./emsdk install --build=Release sdk-incoming-64bit binaryen-master-64bit
./emsdk activate --global --build=Release sdk-incoming-64bit binaryen-master-64bit

emcc -O3 -s WASM=1 -s EXTRA_EXPORTED_RUNTIME_METHODS='["cwrap"]' 'fibo.c'
```

You will obtain the binary `a.out.asm` and the JS wrapper `a.out.js`.

*On Windows*: remove the `./` in the commands.

# Call it in your browser

index.html
```javascript
<script src="a.out.js"></script>
<script>
  Module.onRuntimeInitialized = _ => {
    const fib = Module.cwrap('fib', 'number', ['number']);
    console.log(fib(12));
  };
</script>
```

If you try it, you will have a CORS error, so you need to access it from a server.
```
npm install http-server -g
http-server
```

Now you can go to `http://127.0.0.1:8080/index.html` and see the result in your console.
