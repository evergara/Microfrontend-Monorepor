# Cms

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 15.1.6.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The application will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via a platform of your choice. To use this command, you need to first add a package that implements end-to-end testing capabilities.

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI Overview and Command Reference](https://angular.io/cli) page.


# Demo Microfront Angular 15 con Module Federation

## Creación de espacio de trabajo

Para esta demostración se usa un multi project de Angular, primero debes crear un espacio de trabajo, en este caso se llama **"cms"**

```console
ng new cms --create-application=false
```

Luego crearemos los siguientes proyectos:

## cms-shell

Este proyecto sera nuestro **contenedor o shell** de microfrontend

```console
ng generate application cms-shell --style=scss --routing=true
```

## mf-cms-user

```console
ng generate application mf-cms-user --style=scss --routing=true
```

# Activación de la federación de módulos para proyectos angular

El paquete **@angular-architects/module-federation** proporciona un generador personalizado. Si deseas aprender más de esta librería y arquitectura Angular visita el siguiente link:
https://www.angulararchitects.io/en/aktuelles/the-microfrontend-revolution-module-federation-in-webpack-5/

```console
npm i -D @angular-architects/module-federation
```

Una vez instalada la librería agregaremos el uso de Module Federation a nuestros MF (microfrontends) y agregaremos unas configuraciones:

```console
ng add @angular-architects/module-federation --project cms-shell --port 4200 --type host
ng add @angular-architects/module-federation --project mf-cms-user --port 4201 --type remote
```

Listo, lo que hara este comando es crear unos archivos **webpack.config.js** en cada uno de nuestros MF para poder hacer uso de la federación de modulos.

Luego solo es cuestion de configurar los MF **"remotos"** y el **"host"**

Ejemplo de configuración para el MF shopping:

```javascript
const {
  shareAll,
  withModuleFederationPlugin,
} = require("@angular-architects/module-federation/webpack");

module.exports = withModuleFederationPlugin({
  name: "mf-cms-user",
  exposes: {
    "./UsersModule":
      "./projects/mf-cms-user/src/app/products/users.module.ts",
  },

  shared: {
    ...shareAll({
      singleton: true,
      strictVersion: true,
      requiredVersion: "auto",
    }),
  },
});
```

# Ejecutar los proyectos en paralelo

Para realizar esto usaremos la librería **npm-run-all**

```console
npm i -D npm-run-all
```

Una vez instalada agregamos lo siguientes **scripts** en nuestro archivo _package.json_:

```json
    "cms-shell": "ng s cms-shel",
    "mf-cms-user": "ng s mf-cms-user",
    "all": "npm-run-all --parallel cms-shell mf-cms-user"
```

Ahora solo queda ejecutar el comando **npm run all**