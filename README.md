# bbs-demo

This template should help get you started developing with Vue 3 in Vite.

I'm trying this approach [installing local module with npm](https://stackoverflow.com/questions/8088795/installing-a-local-module-using-npm) to link in my BBS signature library, in particular use the command:

`npm link ../BBSLibrary`

After not working on this for a while I used `npm unlink @grottonetworking/bbs-signatures` then `npm link @grottonetworking/bbs-signatures` to get things to update. I also ran `npm link` in the main directory of the BBS library project.


Note that it was important to add the line `"exports": "./lib/BBS.js"` to the `package.json` file of the BBS library that I'm developing. This allowed Vite to find it. See [article](https://stackoverflow.com/questions/68572936/what-is-the-difference-between-main-and-module-vs-exports-in-package-json)and they reference [node.js: package entry points](https://nodejs.org/api/packages.html#package-entry-points).

## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur) + [TypeScript Vue Plugin (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin).

## Customize configuration

See [Vite Configuration Reference](https://vitejs.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```
