```
/eslint-test/node_modules/estraverse/estraverse.js:517
                        throw new Error('Unknown node type ' + nodeType + '.');
                        ^

Error: Unknown node type Super.
    at Controller.traverse (/eslint-test/node_modules/estraverse/estraverse.js:517:31)
    at EventEmitter.module.exports.api.verify (/eslint-test/node_modules/eslint/lib/eslint.js:779:24)
    at processText (/eslint-test/node_modules/eslint/lib/cli-engine.js:225:27)
    at processFile (/eslint-test/node_modules/eslint/lib/cli-engine.js:262:18)
    at executeOnFile (/eslint-test/node_modules/eslint/lib/cli-engine.js:671:23)
    at /eslint-test/node_modules/eslint/lib/cli-engine.js:695:17
    at Array.forEach (native)
    at CLIEngine.executeOnFiles (/eslint-test/node_modules/eslint/lib/cli-engine.js:693:18)
    at Object.cli.execute (/eslint-test/node_modules/eslint/lib/cli.js:161:95)
    at Object.<anonymous> (/eslint-test/node_modules/eslint/bin/eslint.js:61:20)
```

## To repro:

1. `npm install` with npm@3.3.6
2. run `eslint index.js`

## What's happening:

npm@3's deduping is creating a `node_modules` structure that looks like this:

```
node_modules
├── estraverse@1.9.3
├── estravere-fb@1.3.1
└── eslint@1.8.0
    └── estraverse@4.1.1
```

When eslint requires `estravere-fb`, `estravere-fb` then requires `estraverse` but instead of getting v4.1.1, it gets v1.9.3.

Though it's strange to depend on `estraverse@1.9.3`, that's just to provide a simple repro case. The same structural problems occur in a project that depends on browserify.
