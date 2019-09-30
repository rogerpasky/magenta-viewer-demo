# magenta-viewer-demo
A Magenta-js viewer demo to check some TS compiling problems with es6

This repo is almost an exact copy of `magenta-js/music` but:

* The `package.json` file has been adapted to include the dependency to `"@magenta/music": "1.11.2",` to retreive the npm module and test its usage.
* The `src` directory has been reduced to the bare bones with a minimal `foo()` function module and some extras (index, lib and proto*) which have been adapted to make it complile.
* The `demos` directory has been reduced to the bare bones as well leaving just the `visualizer*` demo and related files.
* All code dependencies in demo files to `../src` have been **replaced** to `@magenta/music`. This is the **key change**.
* There are some minor changes due there's some extra tightness in tslint definitions (solved declaring some `any` varibles to explicit types).

So, remaining code should create with same compiling configuration as magenta-js a useless library (not used at all) and a visualizer demo consuming the `@magenta/music` npm module.

Actually, it builds Ok, but it fails when building the demo as you can see in following example:

```
MacPasky:magenta-viewer-demo Pasky$ yarn run-demos
yarn run v1.17.3
$ webpack-dev-server --config ./webpack/es5.demo.config.ts
ℹ ｢wds｣: Project is running at http://localhost:8080/
ℹ ｢wds｣: webpack output is served from /
ℹ ｢wds｣: Content not from webpack is served from /Users/Pasky/src/magenta-viewer-demo/magenta-viewer-demo/demos
✖ ｢wdm｣: Hash: 5c79d3ca0c0c744fece0
Version: webpack 4.41.0
Time: 3464ms
Built at: 30/09/2019 23:53:09
                         Asset       Size      Chunks             Chunk Names
      ../es5/demos/common.d.ts  666 bytes              [emitted]  
  ../es5/demos/visualizer.d.ts   11 bytes              [emitted]  
           ../es5/src/foo.d.ts   37 bytes              [emitted]  
         ../es5/src/index.d.ts   23 bytes              [emitted]  
           ../es5/src/lib.d.ts   11 bytes              [emitted]  
      ../es5/src/protobuf.d.ts   34 bytes              [emitted]  
../es5/src/protobuf/index.d.ts  190 bytes              [emitted]  
          visualizer_bundle.js   3.94 MiB  visualizer  [emitted]  visualizer
Entrypoint visualizer = visualizer_bundle.js
[0] multi (webpack)-dev-server/client?http://localhost:8080 ./demos/visualizer.ts 40 bytes {visualizer} [built]
[./demos/common.ts] 16.8 KiB {visualizer} [built]
[./demos/visualizer.ts] 2.21 KiB {visualizer} [built]
[./node_modules/@magenta/music/es6/index.js] 920 KiB {visualizer} [built]
[./node_modules/Tone/build/Tone.js] 284 KiB {visualizer} [built]
[./node_modules/ansi-html/index.js] 4.16 KiB {visualizer} [built]
[./node_modules/strip-ansi/index.js] 161 bytes {visualizer} [built]
[./node_modules/webpack-dev-server/client/index.js?http://localhost:8080] (webpack)-dev-server/client?http://localhost:8080 4.29 KiB {visualizer} [built]
[./node_modules/webpack-dev-server/client/overlay.js] (webpack)-dev-server/client/overlay.js 3.51 KiB {visualizer} [built]
[./node_modules/webpack-dev-server/client/socket.js] (webpack)-dev-server/client/socket.js 1.53 KiB {visualizer} [built]
[./node_modules/webpack-dev-server/client/utils/createSocketUrl.js] (webpack)-dev-server/client/utils/createSocketUrl.js 2.89 KiB {visualizer} [built]
[./node_modules/webpack-dev-server/client/utils/log.js] (webpack)-dev-server/client/utils/log.js 964 bytes {visualizer} [built]
[./node_modules/webpack-dev-server/client/utils/reloadApp.js] (webpack)-dev-server/client/utils/reloadApp.js 1.59 KiB {visualizer} [built]
[./node_modules/webpack-dev-server/client/utils/sendMessage.js] (webpack)-dev-server/client/utils/sendMessage.js 402 bytes {visualizer} [built]
[./node_modules/webpack/hot sync ^\.\/log$] (webpack)/hot sync nonrecursive ^\.\/log$ 170 bytes {visualizer} [built]
    + 21 hidden modules

ERROR in ./node_modules/@magenta/music/es6/index.js
Module not found: Error: Can't resolve 'tf' in '/Users/Pasky/src/magenta-viewer-demo/magenta-viewer-demo/node_modules/@magenta/music/es6'
 @ ./node_modules/@magenta/music/es6/index.js 1:82-95
 @ ./demos/visualizer.ts
ℹ ｢wdm｣: Failed to compile.
```

This failure `Module not found: Error: Can't resolve 'tf' in '/Users/Pasky/src/magenta-viewer-demo/magenta-viewer-demo/node_modules/@magenta/music/es6'` claims there's an internal dependence in `@magenta/music` to `@tensorflow/tfjs` (imported internally as `tf`) which has not been declared or resolved properly in the `@magenta/music/es6` package.

Finaly, looking to `./node_modules/@magenta/music/es6/index.js` file, you can see at the beginning it is been used the `require()` statement, which I thought it should be used only in es5, not in es6 (but I'm not an expert in this subject).

``` javascript
!function(t,e){"object"==typeof exports&&"object"==typeof module?module.exports=e(require("tf"),require("Tone")) ...
```

Please feel free to clone this repo to dig into the details if needed.