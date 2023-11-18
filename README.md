# Monorepo with TypeScript and NPM workspaces

> Source: <https://earthly.dev/blog/setup-typescript-monorepo/>

## Initial Setup

Assuming the project folder is `demo`

1. Create folders:

    ```bash
    export PROJ_FOLDER=demo
    mkdir -p $PROJ_FOLDER/src
    mkdir -p $PROJ_FOLDER/packages
    ```

2. Initialize NPM:

    ```bash
    cd $PROJ_FOLDER
    npm init -y
    ```

3. To support workspaces, in `package.json` add:

    ```json
    "workspaces": [
      "packages/*"
    ]
    ```

## Supporting TypeScript

```bash
npm install typescript
npm install --save-dev @types/node ts-node
npm install --save-dev eslint prettier @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

`.eslintrc.json`:

```json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended"
  ],
  "parser": "@typescript-eslint/parser",
  "plugins": [
    "@typescript-eslint"
  ]
}
```

`.prettierrc.json`:

```json
{
  "trailingComma": "all",
  "tabWidth": 2,
  "printWidth": 120,
  "semi": false,
  "singleQuote": false,
  "bracketSpacing": true
}
```


## Initializing a package

```bash
# Format
npm init --scope @<NAMESPACE> --workspace <PATH_TO_PACKAGE> -y

## Example
npm init --scope @monorepo --workspace ./packages/utils -y
```

Add `tsconfig.json` to configure how the TypeScript files are compiled

```json
{
  "compilerOptions": {
    "target": "es2022",
    "module": "commonjs",
    "moduleResolution": "node",
    "declaration": true,
    "strict": true,
    "incremental": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "rootDir": "./src",
    "outDir": "./build",
    "composite": true
  },
  "ts-node": {
    "esm": true,
    "experimentalSpecifierResolution": "node"
  }
}
```

Add build script:

```json
{
  ...
  "scripts": {
    "build": "tsc --build"
  }
  ...
}
```

### Gotchas

1. Ensure that we are exposing the compiled version of the package file:

    ```json
    {
      ...
      "main": "build/index.js",
      ...
    }
    ```

## Global TypeScript Config file

`tsconfig.json` on the root folder:

```json
{
  "compilerOptions": {
    "incremental": true,
    "target": "es2022",
    "module": "commonjs",
    "declaration": true,
    "strict": true,
    "moduleResolution": "node",
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "rootDir": "./src",
    "outDir": "./build"
  },
  "files": [
  ],
  "references": [
    {
      "path": "./packages/utils"
    }
  ],
  "ts-node": {
    "esm": true,
    "experimentalSpecifierResolution": "node"
  }
}
```

Remember to update `files` with all the files you are compiling under `src`. Also remember to update `references` for all the monorepo packages.

## Use a local package inside another local package

Run this command in the root folder:

```bash
npm install @monorepo/utils --workspace ./packages/ui
```

Note the scope (eg. `@monorepo`) and package name `utils`. And then the `workspace` - which package you are targetting.

## Installing an external NPM package to a local package

Run this command in the root folder:

```bash
npm install moment --workspace ./packages/ui
```

Note the `workspace` - which package you are targetting.