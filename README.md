# Plantilla de Aplicación Shopify - Remix

Esta es una plantilla para construir una [aplicación Shopify](https://shopify.dev/docs/apps/getting-started) utilizando el framework [Remix](https://remix.run).

En lugar de clonar este repositorio, puedes utilizar tu gestor de paquetes preferido y la CLI de Shopify con [estos pasos](https://shopify.dev/docs/apps/getting-started/create).

Visita la [documentación de `shopify.dev`](https://shopify.dev/docs/api/shopify-app-remix) para más detalles sobre el paquete de aplicaciones Remix.

## Inicio rápido

### Prerrequisitos

Antes de comenzar, necesitarás lo siguiente:

1. **Node.js**: [Descárgalo e instálalo](https://nodejs.org/en/download/) si aún no lo has hecho.
2. **Cuenta de Socio de Shopify**: [Crea una cuenta](https://partners.shopify.com/signup) si no tienes una.
3. **Tienda de Pruebas**: Configura una [tienda de desarrollo](https://help.shopify.com/en/partners/dashboard/development-stores#create-a-development-store) o una [tienda sandbox de Shopify Plus](https://help.shopify.com/en/partners/dashboard/managing-stores/plus-sandbox-store) para probar tu aplicación.

### Configuración

Si utilizaste la CLI para crear la plantilla, puedes omitir esta sección.

Utilizando yarn:

```shell
yarn install
```

Utilizando npm:

```shell
npm install
```

Utilizando pnpm:

```shell
pnpm install
```

### Desarrollo Local

Utilizando yarn:

```shell
yarn dev
```

Utilizando npm:

```shell
npm run dev
```

Utilizando pnpm:

```shell
pnpm run dev
```

Presiona P para abrir la URL de tu aplicación. Una vez que hagas clic en instalar, puedes comenzar el desarrollo.

El desarrollo local está impulsado por [la CLI de Shopify](https://shopify.dev/docs/apps/tools/cli). Inicia sesión en tu cuenta de socio, conecta una aplicación, proporciona variables de entorno, actualiza la configuración remota, crea un túnel y proporciona comandos para generar extensiones.

### Autenticación y consulta de datos

Para autenticar y consultar datos, puedes utilizar la constante `shopify` que se exporta desde `/app/shopify.server.js`:

```js
export async function loader({ request }) {
  const { admin } = await shopify.authenticate.admin(request);

  const response = await admin.graphql(`
    {
      products(first: 25) {
        nodes {
          title
          description
        }
      }
    }`);

  const {
    data: {
      products: { nodes },
    },
  } = await response.json();

  return json(nodes);
}
```

Esta plantilla viene preconfigurada con ejemplos de:

1. Configuración de tu aplicación Shopify en [/app/shopify.server.ts](https://github.com/Shopify/shopify-app-template-remix/blob/main/app/shopify.server.ts)
2. Consulta de datos utilizando GraphQL. Por favor, ve: [/app/routes/app.\_index.tsx](https://github.com/Shopify/shopify-app-template-remix/blob/main/app/routes/app._index.tsx).
3. Respuesta a webhooks obligatorios en [/app/routes/webhooks.tsx](https://github.com/Shopify/shopify-app-template-remix/blob/main/app/routes/webhooks.tsx)

Por favor, lee la [documentación para @shopify/shopify-app-remix](https://www.npmjs.com/package/@shopify/shopify-app-remix#authenticating-admin-requests) para entender qué otras API están disponibles.

## Implementación

### Almacenamiento de la Aplicación

Esta plantilla utiliza [Prisma](https://www.prisma.io/) para almacenar datos de sesión, utilizando por defecto una base de datos [SQLite](https://www.sqlite.org/index.html).
La base de datos está definida como un esquema Prisma en `prisma/schema.prisma`.

Este uso de SQLite funciona en producción si tu aplicación se ejecuta como una única instancia.
La base de datos que funcione mejor para ti depende de los datos que tu aplicación necesite y de cómo se consulten.
Puedes ejecutar tu base de datos preferida en un servidor tú mismo o alojarla con una empresa de SaaS.
Aquí tienes una lista corta de proveedores de bases de datos que ofrecen un nivel gratuito para comenzar:

| Base de Datos   | Tipo             | Proveedores                                                                                                                                                                                                                               |
| ---------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| MySQL      | SQL              | [Digital Ocean](https://www.digitalocean.com/try/managed-databases-mysql), [Planet Scale](https://planetscale.com/), [Amazon Aurora](https://aws.amazon.com/rds/aurora/), [Google Cloud SQL](https://cloud.google.com/sql/docs/mysql) |
| PostgreSQL | SQL              | [Digital Ocean](https://www.digitalocean.com/try/managed-databases-postgresql), [Amazon Aurora](https://aws.amazon.com/rds/aurora/), [Google Cloud SQL](https://cloud.google.com/sql/docs/postgres)                                   |
| Redis      | Key-value        | [Digital Ocean](https://www.digitalocean.com/try/managed-databases-redis), [Amazon MemoryDB](https://aws.amazon.com/memorydb/)                                                                                                        |
| MongoDB    | NoSQL / Document | [Digital Ocean](https://www.digitalocean.com/try/managed-databases-mongodb), [MongoDB Atlas](https://www.mongodb.com/atlas/database)                                                                                                  |

Para utilizar uno de estos, puedes utilizar un [proveedor de origen de datos](https://www.prisma.io/docs/reference/api-reference/prisma-schema-reference#datasource) diferente en tu archivo `schema.prisma`, o un [paquete de adaptador de almacenamiento de sesión](https://github.com/Shopify/shopify-api-js/blob/main/packages/shopify-api/docs/guides/session-storage.md) diferente.

### Compilación

Remix maneja la compilación de la aplicación por ti, ejecutando el siguiente comando con el gestor de paquetes que elijas:

Utilizando yarn:

```shell
yarn build
```

Utilizando npm:

```shell
npm run build
```

Utilizando pnpm:

```shell
pnpm run build
```

## Alojamiento

Cuando estés listo para configurar tu aplicación en producción, puedes seguir [nuestra documentación de implementación](https://shopify.dev/docs/apps/deployment/web) para alojar tu aplicación en un proveedor de la nube como [Heroku](https://www.heroku.com/) o [Fly.io](https://fly.io/).

Cuando llegues al paso para [configurar variables de entorno](https://shopify.dev/docs/apps/deployment/web#set-env-vars), también necesitas establecer la variable `NODE_ENV=production`.

### Alojamiento en Vercel

Cuando alojas

 tu aplicación Shopify Remix en Vercel, Vercel utiliza un fork de la [biblioteca Remix](https://github.com/vercel/remix).

Para asegurarte de que todas las variables globales se establezcan correctamente cuando despliegues tu aplicación en Vercel, actualiza tu aplicación para utilizar el adaptador de Vercel en lugar del adaptador de nodo.

```diff
// shopify.server.ts
- import "@shopify/shopify-app-remix/adapters/node";
+ import "@shopify/shopify-app-remix/adapters/vercel";
```

## Problemas comunes / Solución de problemas

### Las tablas de la base de datos no existen

Si recibes este error:

```
La tabla 'main.Session' no existe en la base de datos actual.
```

Necesitas crear la base de datos para Prisma. Ejecuta el script `setup` en `package.json` utilizando tu gestor de paquetes preferido.

### Navegación/Redirección rompe una aplicación incrustada

Las aplicaciones incrustadas de Shopify deben mantener la sesión de usuario, lo que puede ser complicado dentro de un iFrame. Para evitar problemas:

1. Utiliza `Link` de `@remix-run/react` o `@shopify/polaris`. No utilices `<a>`.
2. Utiliza el ayudante `redirect` devuelto desde `authenticate.admin`. No utilices `redirect` de `@remix-run/node`.
3. Utiliza `useSubmit` o `<Form/>` de `@remix-run/react`. No utilices un `<form/>` en minúsculas.

Esto solo se aplica si tu aplicación está incrustada, que será el caso por defecto.

### No incrustado

Las aplicaciones de Shopify son mejores cuando están incrustadas en el Administrador de Shopify. Esta plantilla está configurada de esa manera. Si tienes una razón para no incrustar tu aplicación, por favor realiza 2 cambios:

1. Cambia la propiedad `isEmbeddedApp` a false para el `AppProvider` en `/app/routes/app.jsx`
2. Elimina cualquier uso de las API de App Bridge (`window.shopify`) de tu código
3. Actualiza la configuración para shopifyApp en `app/shopify.server.js`. Pasa `isEmbeddedApp: false`

### OAuth entra en un bucle cuando cambio los alcances de mi aplicación

Si cambias los alcances de tu aplicación y la autenticación entra en un bucle y falla con un mensaje de Shopify de que se intentó demasiadas veces, es posible que hayas olvidado actualizar tus alcances con Shopify.
Para hacerlo, puedes ejecutar el comando `deploy` CLI.

Utilizando yarn:

```shell
yarn deploy
```

Utilizando npm:

```shell
npm run deploy
```

Utilizando pnpm:

```shell
pnpm run deploy
```

### Mis suscripciones de webhook no se están actualizando

Esta plantilla registra webhooks después de que OAuth se completa, utilizando el gancho `afterAuth` al llamar a `shopifyApp`.
El paquete llama a ese gancho en 2 escenarios:
- Después de instalar la aplicación
- Cuando un token de acceso caduca

Durante el desarrollo normal, la aplicación no necesitará reautenticarse la mayor parte del tiempo, por lo que las suscripciones no se actualizan.

Para forzar a tu aplicación a actualizar las suscripciones, puedes desinstalarla y reinstalarla en tu tienda de desarrollo.
Eso forzará el proceso de OAuth y llamará al gancho `afterAuth`.

### Webhook de administrador creado fallando en la validación HMAC

Las suscripciones de webhook creadas en el [administrador de Shopify](https://help.shopify.com/en/manual/orders/notifications/webhooks) fallarán en la validación HMAC. Esto se debe a que el payload del webhook no está firmado con la clave secreta de tu aplicación.

Crea [suscripciones de webhook]((https://shopify.dev/docs/api/shopify-app-remix/v1/guide-webhooks)) utilizando el objeto `shopifyApp` en su lugar.

Prueba tus webhooks con el [Shopify CLI](https://shopify.dev/docs/apps/tools/cli/commands#webhook-trigger) o disparando eventos manualmente en el administrador de Shopify (por ejemplo, actualizando el título del producto para desencadenar un `PRODUCTS_UPDATE`).

### Sugerencias de GraphQL incorrectas

Por defecto, la [extensión graphql.vscode-graphql](https://marketplace.visualstudio.com/items?itemName=GraphQL.vscode-graphql) para VS Code asumirá que las consultas o mutaciones de GraphQL son para la [API de administración de Shopify](https://shopify.dev/docs/api/admin). Esta es una configuración sensata por defecto, pero puede que no sea cierta si:

1. Utilizas otra API de Shopify como la API de la tienda.
2. Utilizas una API de GraphQL de terceros.

En esta situación, por favor actualiza la configuración de [.graphqlrc.ts](https://github.com/Shopify/shopify-app-template-remix/blob/main/.graphqlrc.ts).

### El primer parámetro tiene un miembro 'readable' que no es un ReadableStream.

Ver [alojamiento en Vercel](#hosting-on-vercel).

### Objeto de administrador no definido en eventos de webhook desencadenados por la CLI

Cuando desencadenas un evento de webhook usando la CLI de Shopify, el objeto `admin` será `undefined`. Esto se debe a que la CLI desencadena un evento con una tienda válida, pero no existente. El objeto `admin` solo está disponible cuando el webhook es desencadenado por una tienda que ha instalado la aplicación.

Los webhooks desencadenados por la CLI están destinados para la experimentación inicial y la prueba de tu configuración de webhook. Para obtener más información sobre cómo probar tus webhooks, consulta la [documentación de la CLI de Shopify](https://shopify.dev/docs/apps/tools/cli/commands#webhook-trigger).

## Beneficios

Las aplicaciones de Shopify se construyen sobre una variedad de herramientas de Shopify para crear una gran experiencia para los comerciantes.

<!-- TODO: Descomenta esto después de haber actualizado la documentación -->
<!-- El tutorial [create an app](https://shopify.dev/docs/apps/getting-started/create) en nuestra documentación para desarrolladores te guiará a través de la creación de una aplicación Shopify utilizando esta plantilla. -->

La plantilla de aplicación Remix viene con la siguiente funcionalidad lista para usar:

- [OAuth](https://github.com/Shopify/shopify-app-js/tree/main/packages/shopify-app-remix#authenticating-admin-requests): Instalación de la aplicación y otorgamiento de permisos
- [GraphQL Admin API](https://github.com/Shopify/shopify-app-js/tree/main/packages/shopify-app-remix#using-the-shopify-admin-graphql-api): Consulta

 o mutación de datos de administración de Shopify
- [REST Admin API](https://github.com/Shopify/shopify-app-js/tree/main/packages/shopify-app-remix#using-the-shopify-admin-rest-api): Clases de recursos para interactuar con la API
- [Webhooks](https://github.com/Shopify/shopify-app-js/tree/main/packages/shopify-app-remix#authenticating-webhook-requests): Devoluciones de llamada enviadas por Shopify cuando ocurren ciertos eventos
- [AppBridge](https://shopify.dev/docs/api/app-bridge): Esta plantilla utiliza la próxima generación de la biblioteca Shopify App Bridge que funciona en armonía con las versiones anteriores.
- [Polaris](https://polaris.shopify.com/): Sistema de diseño que permite a las aplicaciones crear experiencias similares a Shopify

## Pila Tecnológica

Esta plantilla utiliza [Remix](https://remix.run). Las siguientes herramientas de Shopify también están incluidas para facilitar el desarrollo de la aplicación:

- [Shopify App Remix](https://shopify.dev/docs/api/shopify-app-remix) proporciona autenticación y métodos para interactuar con las APIs de Shopify.
- [Shopify App Bridge](https://shopify.dev/docs/apps/tools/app-bridge) permite que tu aplicación se integre perfectamente dentro del Administrador de Shopify.
- [Polaris React](https://polaris.shopify.com/) es un potente sistema de diseño y biblioteca de componentes que ayuda a los desarrolladores a construir experiencias consistentes y de alta calidad para los comerciantes de Shopify.
- [Webhooks](https://github.com/Shopify/shopify-app-js/tree/main/packages/shopify-app-remix#authenticating-webhook-requests): Devoluciones de llamada enviadas por Shopify cuando ocurren ciertos eventos
- [Polaris](https://polaris.shopify.com/): Sistema de diseño que permite a las aplicaciones crear experiencias similares a Shopify

## Recursos

- [Documentación de Remix](https://remix.run/docs/en/v1)
- [Shopify App Remix](https://shopify.dev/docs/api/shopify-app-remix)
- [Introducción a las aplicaciones de Shopify](https://shopify.dev/docs/apps/getting-started)
- [Autenticación de la aplicación](https://shopify.dev/docs/apps/auth)
- [CLI de Shopify](https://shopify.dev/docs/apps/tools/cli)
- [Extensiones de la aplicación](https://shopify.dev/docs/apps/app-extensions/list)
- [Funciones de Shopify](https://shopify.dev/docs/api/functions)
- [Comenzando con la internacionalización de tu aplicación](https://shopify.dev/docs/apps/best-practices/internationalization/getting-started)