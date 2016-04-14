<properties 
	pageTitle="Configuración de Python con Aplicaciones web del Servicio de aplicaciones de Azure" 
	description="En este tutorial se describen las opciones para crear y configurar una aplicación Python básica compatible con la Interfaz de puerta de enlace de servicio web (WSGI) en Aplicaciones web del Servicio de aplicaciones de Azure." 
	services="app-service" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="huvalo"/>




# Configuración de Python con Aplicaciones web del Servicio de aplicaciones de Azure

Este tutorial describen las opciones para crear y configurar una aplicación básica de interfaz de puerta de enlace de servidor web (WSGI) compatible con Python en [aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Describe características adicionales de implementación de Git como, por ejemplo, el entorno virtual y la instalación del paquete mediante requirements.txt.


## ¿Bottle, Django o Flask?

Azure Marketplace contiene plantillas para los marcos de Bottle, Django y Flask. Si está desarrollando su primera aplicación web en el Servicio de aplicaciones de Azure, o no está familiarizado con Git, le recomendamos que siga uno de estos tutoriales, que incluyen instrucciones paso a paso para compilar una aplicación que funciona de la galería mediante la implementación de Git desde Windows o Mac:

- [Creación de Aplicaciones web con Bottle](web-sites-python-create-deploy-bottle-app.md)
- [Creación de Aplicaciones web con Django](web-sites-python-create-deploy-django-app.md)
- [Creación de Aplicaciones web con Flask](web-sites-python-create-deploy-flask-app.md)


## Creación de una aplicación web en el Portal de Azure

En este tutorial se asume que existe una suscripción a Azure y que ya se tiene acceso al Portal de Azure.

Si no tiene una aplicación web, puede crearla en el [Portal de Azure](https://portal.azure.com). Haga clic en el botón NUEVO que aparece en la esquina superior izquierda y, luego, haga clic en **Web + móvil** > **Aplicación web**.

## Publicación Git

Configure la publicación de Git para la aplicación web recién creada siguiendo las instrucciones de [Implementación continua con GIT en el Servicio de aplicaciones de Azure](web-sites-publish-source-control.md). Este tutorial usa Git para crear, administrar y publicar nuestra aplicación web Python en Aplicaciones web de Azure.

Después de configurar la publicación Git, se creará un repositorio Git y se asociará a la aplicación web. La URL del repositorio se mostrará y, en adelante, se podrá usar para enviar datos del entorno de desarrollo local a la nube. Para publicar aplicaciones a través de Git, asegúrese de que también se haya instalado un cliente Git y use las instrucciones facilitadas para enviar el contenido de la aplicación web al Servicio de aplicaciones de Azure.


## Información general de la aplicación

En las secciones siguientes, se crean los siguientes archivos. Se deben colocar en la raíz del repositorio de Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## Controlador de WSGI

WSGI es un estándar de Python descrito por [PEP 3333](http://www.python.org/dev/peps/pep-3333/) que define una interfaz entre el servidor web y Python. Ofrece una interfaz normalizada para escribir varios marcos de trabajo y aplicaciones web con Python. Los marcos de trabajo web conocidos de Python usan WSGI hoy en día. Aplicaciones web del Servicio de aplicaciones de Azure admite tales marcos de trabajo; además, los usuarios avanzados incluso pueden crear los suyos propios siempre que el controlador personalizado siga las instrucciones de la especificación de WSGI.

A continuación se muestra un ejemplo de `app.py` que define un controlador personalizado:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Puede ejecutar esta aplicación localmente con `python app.py` e ir a `http://localhost:5555` en el explorador web.


## Entorno virtual

Aunque la aplicación de ejemplo anterior no requiere ningún paquete externo, es probable que la aplicación requiera alguno.

Para ayudar a administrar las dependencias de paquete externo, la implementación de Git de Azure admite la creación de entornos virtuales.

Cuando Azure detecta un archivo requirements.txt en la raíz del repositorio, crea automáticamente un entorno virtual denominado `env`. Esto solo ocurre en la primera implementación, o durante cualquier implementación que ocurra después de que cambie el tiempo de ejecución de Python seleccionado.

Puede que quiera crear un entorno virtual localmente para desarrollo, pero no incluirlo en el repositorio de Git.


## Administración de paquetes

Los paquetes enumerados en requirements.txt se instalarán automáticamente en el entorno virtual con pip. Esto se realiza en cada implementación, pero pip omitirá la instalación si un paquete ya está instalado.

Ejemplo `requirements.txt`:

    azure==0.8.4


## versión de Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Ejemplo `runtime.txt`:

    python-2.7


## Web.config

Necesitará crear un archivo web.config para especificar cómo el servidor debe controlar las solicitudes.

Tenga en cuenta que si tiene un archivo web.x.y.config en el repositorio, donde x.y coincide con el tiempo de ejecución de Python seleccionado, Azure copiará automáticamente el archivo correspondiente como web.config.

Los siguientes ejemplos de web.config se basan en un script de proxy del entorno virtual, que se describe en la sección siguiente. Funcionan con el controlador WSGI que se usa en el ejemplo `app.py` anterior.

Ejemplo `web.config` para Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Ejemplo `web.config` para Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Los archivos estáticos los administra el servidor web directamente, sin pasar por el código de Python, para obtener un rendimiento mejorado.

En los ejemplos anteriores, la ubicación de los archivos estáticos en disco debe coincidir con la ubicación de la dirección URL. Esto significa que una solicitud de `http://pythonapp.azurewebsites.net/static/site.css` servirá el archivo en disco en `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER` es donde especifica el controlador WSGI. En los ejemplos anteriores, es `app.wsgi_app` porque el controlador es una función denominada `wsgi_app` en `app.py` en la carpeta raíz.

Se puede personalizar `PYTHONPATH`, pero si instala todas las dependencias en el entorno virtual especificándolas en requirements.txt, no es necesario cambiarlo.


## Proxy del entorno virtual

El siguiente script se utiliza para recuperar el controlador de WSGI, activar el entorno virtual y registrar errores. Está diseñado para ser genérico y se usa sin modificaciones.

Contenido de `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')
    
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)
    
        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()
    
        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))
    
        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
    
        site.main()
    
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## Personalización de la implementación de Git

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## Solución de problemas - Instalación de un paquete

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## Solución de problemas - Entorno virtual

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Python](/develop/python/).

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)





 

<!---HONumber=AcomDC_0302_2016-->