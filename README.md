# Style Guide for Frontend Development

## Introduction

This repository is the home of my style guide, which includes configs for
popular linting and styling tools.

It's directly inspired by the [Vercel Style Guide]('https://vercel.com/design').

The following configs are available, and are designed to be used together.

- [Prettier](#prettier)
- [ESLint](#eslint)
- [TypeScript](#typescript)

## Installation

All of our configs are contained in one package, `pol-standard`. To install:

```sh
# If you use npm
npm i --save-dev pol-standard

# If you use pnpm
pnpm i --save-dev pol-standard

# If you use Yarn
yarn add --dev pol-standard
```

Some of our ESLint configs require peer dependencies. We'll note those
alongside the available configs in the [ESLint](#eslint) section.

## Prettier

> Note: Prettier is a peer-dependency of this package, and should be installed
> at the root of your project.
>
> See: https://prettier.io/docs/en/install.html

To use the shared Prettier config, set the following in `package.json`.

```json
{
  "prettier": "pol-standard/prettier"
}
```

## ESLint

> Note: ESLint is a peer-dependency of this package, and should be installed
> at the root of your project.
>
> See: https://eslint.org/docs/user-guide/getting-started#installation-and-usage

This ESLint config is designed to be composable.

The following base configs are available. You can use one or both of these
configs, but they should always be first in `extends`:

- `pol-standard/eslint/browser`
- `pol-standard/eslint/node`

Note that you can scope configs, so that configs only target specific files.
For more information, see: [Scoped configuration with `overrides`](#scoped-configuration-with-overrides).

The following additional configs are available:

- `pol-standard/eslint/jest`
- `pol-standard/eslint/jest-react` (includes rules for `@testing-library/react`)
- `pol-standard/eslint/next` (requires `@next/eslint-plugin-next` to be installed at the same version as `next`)
- `pol-standard/eslint/playwright-test`
- `pol-standard/eslint/react`
- `pol-standard/eslint/typescript` (requires `typescript` to be installed and [additional configuration](#configuring-eslint-for-typescript))
- `pol-standard/eslint/vitest`

> You'll need to use `require.resolve` to provide ESLint with absolute paths,
> due to an issue around ESLint config resolution (see
> [eslint/eslint#9188](https://github.com/eslint/eslint/issues/9188)).

For example, use the shared ESLint config(s) in a react project, set the
following in `.eslintrc.js`.

```js
// Standard eslint config for next.js projects
module.exports = {
  extends: [
    require.resolve('pol-standard/eslint/browser'),
    require.resolve('pol-standard/eslint/react'),
    require.resolve('pol-standard/eslint/typescript'),
  ],
  overrides: [
    {
      files: ['**/__tests__/**/*.[jt]s?(x)', '**/?(*.)+(spec|test).[jt]s?(x)'],
      extends: [
        require.resolve('pol-standard/eslint/jest'),
        require.resolve('pol-standard/eslint/jest-react'),
        // or if you use Vitest:
        require.resolve('pol-standard/eslint/vitest'),
      ],
    },
  ],
};
```

Or for Next.js projects with TypeScript, set the following in `.eslintrc.js`.

```js
// Standard eslint config for next.js projects
module.exports = {
  extends: [
    require.resolve('pol-standard/eslint/browser'),
    require.resolve('pol-standard/eslint/react'),
    require.resolve('pol-standard/eslint/next'),
  ],
};
```

### Configuring ESLint for TypeScript

Some of the rules enabled in the TypeScript config require additional type
information, you'll need to provide the path to your `tsconfig.json`.

For more information, see: https://typescript-eslint.io/docs/linting/type-linting

```js
const { resolve } = require('node:path');

const project = resolve(__dirname, 'tsconfig.json');

module.exports = {
  root: true,
  extends: [
    require.resolve('pol-standard/eslint/node'),
    require.resolve('pol-standard/eslint/react'),
    require.resolve('pol-standard/eslint/typescript'),
    require.resolve('pol-standard/eslint/browser'),
    require.resolve('pol-standard/eslint/vitest'),
    // ... other configs
  ],
  parserOptions: {
    project,
  },
  settings: {
    'import/resolver': {
      typescript: {
        project,
      },
    },
  },
};
```

### Configuring custom components for `jsx-a11y`

It's common practice for React apps to have shared components like `Button`,
which wrap native elements. You can pass this information along to `jsx-a11y`
via the `components` setting.

The below list is not exhaustive.

```js
module.exports = {
  root: true,
  extends: [require.resolve('pol-standard/eslint/react')],
  settings: {
    'jsx-a11y': {
      components: {
        Article: 'article',
        Button: 'button',
        Image: 'img',
        Input: 'input',
        Link: 'a',
        Video: 'video',
      },
    },
  },
};
```

### Scoped configuration with `overrides`

ESLint configs can be scoped to include/exclude specific paths. This ensures
that rules don't "leak" into places where those rules don't apply.

In this example, Jest rules are only being applied to files matching Jest's
default test match pattern.

```js
module.exports = {
  extends: [require.resolve('pol-standard/eslint/node')],
  overrides: [
    {
      files: ['**/__tests__/**/*.[jt]s?(x)', '**/?(*.)+(spec|test).[jt]s?(x)'],
      extends: [require.resolve('pol-standard/eslint/jest')],
    },
  ],
};
```

#### A note on file extensions

By default, all TypeScript rules are scoped to files ending with `.ts` and
`.tsx`.

However, when using overrides, file extensions must be included or ESLint will
only include `.js` files.

```js
module.exports = {
  overrides: [
    { files: [`directory/**/*.[jt]s?(x)`], rules: { 'my-rule': 'off' } },
  ],
};
```

## TypeScript

This style guide provides multiple TypeScript configs. These configs correlate to the LTS Node.js versions, providing the appropriate `lib`, `module`, `target`, and `moduleResolution` settings for each version. The following configs are available:

Node.js Version

v16 : `pol-standard/typescript/node16`
v18 : `pol-standard/typescript/node18`
v20 : `pol-standard/typescript/node20`

To use the shared TypeScript config, set the following in `tsconfig.json`.

```json
{
  "extends": "pol-standard/typescript/node16"
}
```

The base TypeScript config is also available as [`pol-standard/typescript`](./typescript/tsconfig.base.json) which only specifies a set of general rules. You should inherit from this file when setting custom `lib`, `module`, `target`, and `moduleResolution` settings.
