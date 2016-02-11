<properties
	pageTitle="Aplicación web Node.js del modelo de aplicación v2.0 | Microsoft Azure"
	description="Cómo crear una aplicación web Node JS con la que los usuarios pueden iniciar sesión utilizando tanto la cuenta personal de Microsoft como sus cuentas profesionales o educativas."
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="brandwe"/>

# Vista previa del modelo de aplicaciones v2.0: Agregar inicio de sesión a una aplicación web NodeJS


  >[AZURE.NOTE]Esta información se aplica a la vista previa pública del modelo de aplicaciones v2.0. Para obtener instrucciones sobre cómo integrarse en el servicio Azure AD, disponible con carácter general, consulte la [Guía para desarrolladores de Azure Active Directory](active-directory-developers-guide.md).


Aquí usaremos Passport para:

- Iniciar la sesión del usuario en la aplicación con Azure AD y el modelo de aplicación de la versión 2.0.
- Mostrar información sobre el usuario.
- Cerrar la sesión del usuario en la aplicación.

**Passport** es middleware de autenticación para Node.js. Muy flexible y modular, Passport puede pasar discretamente a cualquier aplicación web basada en Express o Restify. Un conjunto completo de estrategias admite la autenticación mediante un nombre de usuario y contraseña, Facebook, Twitter y mucho más. Hemos desarrollado una estrategia para Microsoft Azure Active Directory. Instalaremos este módulo y, a continuación, agregaremos el complemento `passport-azure-ad` de Microsoft Azure Active Directory.

Para ello, deberá hacer lo siguiente:

1. Registrar una aplicación.
2. Configurar la aplicación para que use la estrategia Passport-azure-ad.
3. Usar Passport para emitir solicitudes de inicio y cierre de sesión en Azure AD.
4. Imprima datos sobre el usuario.

El código de este tutorial se mantiene [en GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Para continuar, puede [descargar el esqueleto de la aplicación como .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) o clonarlo:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

La aplicación completa se ofrece también al final de este tutorial.

## 1\. Registrar una aplicación
Cree una nueva aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com) o siga estos [pasos detallados](active-directory-v2-app-registration.md). Asegúrese de que:

- Anotar el **Id. de aplicación** asignado a su aplicación; lo necesitará pronto.
- Agregar la plataforma **web** para su aplicación.
- Escribir el **URI de redireccionamiento** correcto. El identificador URI de redirección indica a Azure AD a dónde se deben dirigir las respuestas de autenticación; el valor predeterminado para este tutorial es `http://localhost:3000/auth/openid/return`.

## 2\. Incorporación de requisitos previos al directorio

En la línea de comandos, cambie los directorios a la carpeta raíz si aún no está ahí y ejecute los siguientes comandos:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

- Además, hemos usado `passport-azure-ad` para nuestra vista previa en el esqueleto del inicio rápido.

- `npm install passport-azure-ad`


Esto instalará las bibliotecas de las que depende passport-azure-ad.

## 3\. Configuración de la aplicación para que use la estrategia passport-node-js
Aquí configuraremos el middleware Express para usar el protocolo de autenticación OpenID Connect. Passport se usará para emitir solicitudes de inicio y cierre de sesión, administrar la sesión del usuario y obtener información sobre el usuario, entre otras cosas.

-	Para comenzar, abra el archivo `config.js` en la raíz del proyecto y escriba los valores de configuración de la aplicación en la sección `exports.creds`.
    -	El `clientID:` es el **identificador de aplicación** asignado a su aplicación en el portal de registro.
    -	El `returnURL` es el **identificador URI de redireccionamiento** que escribió en el portal.
    - `clientSecret` es el secreto generado en el portal.

- Después, abra `app.js` en la raíz del proyecto y agregue la llamada siguiente para invocar la estrategia `OIDCStrategy` que viene con `passport-azure-ad`.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- Después, use la estrategia a la que acabamos de hacer referencia para administrar las solicitudes de inicio de sesión

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2)
//
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    skipUserProfile: config.creds.skipUserProfile
    //scope: config.creds.scope
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport usa un patrón similar para todas sus estrategias (Twitter, Facebook, etc.) al que se ajustan todos los escritores de estrategias. Si se examina la estrategia, se percibe que se pasa function(), que tiene un token y un done como parámetros. La estrategia volverá diligentemente a nosotros una vez que haya finalizado su labor. Una vez que lo haga, almacenaremos el usuario y guardaremos provisionalmente el token, con el fin de que no tengamos que volver a pedirlo.

> [AZURE.IMPORTANT]El código anterior toma cualquier usuario que se autentique en el servidor. Esto se conoce como registro automático. En los servidores de producción, no debería permitir que acceda cualquier usuario sin que antes pase por el proceso de registro que decida. Este suele ser el patrón que se observa en las aplicaciones de consumidor que permiten registrarse con Facebook, pero que luego piden que se rellene información adicional. Si esta no fuera una aplicación de ejemplo, podríamos haber extraído el correo electrónico del objeto de token que se devuelve y, a continuación, haber pedido al usuario que rellenara la información adicional. Puesto que se trata de un servidor de prueba, simplemente los agregaremos a la base de datos en memoria.

- A continuación, vamos a agregar los métodos que nos permitirán realizar un seguimiento de los usuarios con sesión iniciada, tal como requiere Passport. Esta operación incluye la serialización y deserialización de la información del usuario:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

- A continuación, vamos a agregar el código para cargar el motor exprés. Aquí puede ver que se usa el patrón de /views y /routes predeterminado que proporciona Express.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- Por último, vamos a agregar las rutas POST que entregarán las solicitudes de inicio de sesión reales al motor `passport-azure-ad`:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## 4\. Uso de Passport para emitir solicitudes de inicio y cierre de sesión en Azure AD

La aplicación ya está configurada correctamente para comunicarse con el punto de conexión v2.0 mediante el protocolo de autenticación OpenID Connect. `passport-azure-ad` se ha ocupado de todos los detalles poco atractivos de la elaboración de mensajes de autenticación, validación de tokens de Azure AD y mantenimiento de la sesión del usuario. Ya solo falta ofrecer a los usuarios una forma de iniciar sesión, cerrar sesión y recopilar información adicional sobre el usuario con la sesión iniciada.

- En primer lugar, se agregan el método predeterminado, el de inicio de sesión, el de cuenta y el de cierre de sesión al archivo `app.js`:

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-	Analicemos esto con detalle:
    -	La ruta `/` realizará la redirección a la vista index.ejs pasando el usuario en la solicitud (si existe).
    - La ruta `/account` primero ***se asegurará de que estamos autenticados*** (lo que se implementará a continuación) y, seguidamente, pasará el usuario en la solicitud para que podamos obtener información adicional sobre él.
    - La ruta `/login` llamará al autenticador azuread-openidconnect desde `passport-azuread` y, si no tiene éxito, redirigirá al usuario a /login.
    - `/logout` simplemente llamará a logout.ejs (y a la ruta), que borra las cookies y devuelve el usuario a index.ejs.


- Para la última parte de `app.js`, agregaremos el método EnsureAuthenticated que se usa en `/account`.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

- Por último, crearemos el propio servidor en `app.js`:

```JavaScript

app.listen(3000);

```


## 5\. Creación de las vistas y las rutas en Express para mostrar el usuario en el sitio web

`app.js` está finalizado. Ahora solo es preciso agregar las rutas y vistas que mostrarán la información obtenida al usuario, así como administrar las rutas `/logout` y `/login` creadas.

- Cree la ruta `/routes/index.js` en el directorio raíz.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Creación de la ruta `/routes/user.js` en el directorio raíz

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Estas sencillas rutas solo pasarán la solicitud a nuestras vistas, incluido el usuario, si está presente.

- Cree la vista `/views/index.ejs` en el directorio raíz. Se trata de una página sencilla que llamará a nuestros métodos de inicio y cierre de sesión, y nos permitirá obtener información de la cuenta. Observe que podemos usar el `if (!user)` condicional, ya que el usuario pasado en la solicitud es una evidencia de que tenemos un usuario con la sesión iniciada.

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
	<h2>Hello, <%= user.displayName %>.</h2>
	<a href="/account">Account Info</a></br>
	<a href="/logout">Log Out</a>
<% } %>
```

- Cree la vista `/views/account.ejs` en el directorio raíz de modo que podamos ver la información adicional que `passport-azuread` ha incluido en la solicitud del usuario.

```Javascript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

- Por último, vamos a mejorar la apariencia, para lo que agregaremos un diseño. Cree la vista '/views/layout.ejs' en el directorio raíz

```HTML

<!DOCTYPE html>
<html>
	<head>
		<title>Passport-OpenID Example</title>
	</head>
	<body>
		<% if (!user) { %>
			<p>
			<a href="/">Home</a> |
			<a href="/login">Log In</a>
			</p>
		<% } else { %>
			<p>
			<a href="/">Home</a> |
			<a href="/account">Account</a> |
			<a href="/logout">Log Out</a>
			</p>
		<% } %>
		<%- body %>
	</body>
</html>
```

Por último, compile y ejecute su aplicación.

Ejecute `node app.js` y vaya a `http://localhost:3000`.


Inicie sesión con una cuenta de Microsoft personal o con una cuenta profesional o educativa, y observe cómo se refleja la identidad del usuario en la lista /account. Ahora dispone de una aplicación web protegida mediante protocolos estándar del sector que pueden autenticar a los usuarios tanto con sus cuentas personales como con sus cuentas profesionales/educativas.

##Pasos siguientes

Como referencia, el ejemplo finalizado (sin sus valores de configuración) [se proporciona en forma de archivo .zip aquí](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip), aunque también puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Ahora puede pasar a temas más avanzados. También puede probar lo siguiente:

[Proteger una API web con el modelo de aplicaciones v2.0 en Node.js >>](active-directory-v2-devquickstarts-webapi-nodejs.md)

Para obtener recursos adicionales, consulte: - [la vista previa del modelo de aplicaciones v2.0 >>](active-directory-appmodel-v2-overview.md) - [la etiqueta "azure-active-directory" StackOverflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory).

<!---HONumber=AcomDC_1217_2015-->