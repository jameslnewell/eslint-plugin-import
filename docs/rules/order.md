# Enforce a convention in module import order

Enforce a convention in the order of `require()` / `import` statements. The order is as shown in the following example:

```js
// 1. node "builtins"
import fs from 'fs';
import path from 'path';
// 2. "external" modules
import _ from 'lodash';
import chalk from 'chalk';
// 3. "internal" modules
// (if you have configured your path or webpack to handle your internal paths differently)
import foo from 'src/foo';
// 4. modules from a "parent" directory
import foo from '../foo';
import qux from '../../foo/qux';
// 5. "sibling" modules from the same or a sibling's directory
import bar from './bar';
import baz from './bar/baz';
// 6. "index" of the current directory
import main from './';
```

Unassigned imports are ignored, as the order they are imported in may be important.

Statements using the ES6 `import` syntax must appear before any `require()` statements.


## Fail

```js
import _ from 'lodash';
import path from 'path'; // `path` import should occur before import of `lodash`

// -----

var _ = require('lodash');
var path = require('path'); // `path` import should occur before import of `lodash`

// -----

var path = require('path');
import foo from './foo'; // `import` statements must be before `require` statement
```


## Pass

```js
import path from 'path';
import _ from 'lodash';

// -----

var path = require('path');
var _ = require('lodash');

// -----

// Allowed as ̀`babel-register` is not assigned.
require('babel-register');
var path = require('path');

// -----

// Allowed as `import` must be before `require`
import foo from './foo';
var path = require('path');
```

## Options

This rule supports the following options:

### `groups: [array]`:

How groups are defined, and the order to respect. `groups` must be an array of `string` or [`string`]. The only allowed `string`s are: `"builtin"`, `"external"`, `"internal"`, `"parent"`, `"sibling"`, `"index"`. The enforced order is the same as the order of each element in a group. Omitted types are implicitly grouped together as the last element. Example:
```js
[
  'builtin', // Built-in types are first
  ['sibling', 'parent'], // Then sibling and parent types. They can be mingled together
  'index', // Then the index file
  // Then the rest: internal and external type
]
```
The default value is `["builtin", "external", "internal", "parent", "sibling", "index"]`.

You can set the options like this:

```js
"import/order": ["error", {"groups": ["index", "sibling", "parent", "internal", "external", "builtin"]}]
```

### `newlines-between: [always|never]`:


Enforces or forbids new lines between import groups:

- If omitted, assertion messages will be neither enforced nor forbidden.
- If set to `always`, at least one new line between each group will be enforced, and new lines inside a group will be forbidden. To prevent multiple lines between imports, core `no-multiple-empty-lines` rule can be used.
- If set to `never`, no new lines are allowed in the entire import section.

With the default group setting, the following will be invalid:

```js
/* eslint import/order: ["error", {"newlines-between": "always"}] */
import fs from 'fs';
import path from 'path';
import index from './';
import sibling from './foo';
```

```js
/* eslint import/order: ["error", {"newlines-between": "never"}] */
import fs from 'fs';
import path from 'path';

import index from './';

import sibling from './foo';
```

while those will be valid:

```js
/* eslint import/order: ["error", {"newlines-between": "always"}] */
import fs from 'fs';
import path from 'path';

import index from './';

import sibling from './foo';
```

```js
/* eslint import/order: ["error", {"newlines-between": "never"}] */
import fs from 'fs';
import path from 'path';
import index from './';
import sibling from './foo';
```

## Related

- [`import/external-module-folders`] setting

[`import/external-module-folders`]: ../../README.md#importexternal-module-folders
