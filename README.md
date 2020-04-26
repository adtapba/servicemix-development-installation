# Guía creación instalación ServiceMix para desarrollo de bundles
### Objetivo
Especificar los pasos para crear una instalación de Apache ServiceMix 6.0.0, en la que poder probar bundles de integración desarrollados para la APBA.
### Pasos de instalación
1. Descargar Apache ServiceMix 6.0.0
* Ir a http://servicemix.apache.org/downloads/servicemix-6.0.0.html y descargar el archivo ZIP con la distribución. 

* En la misma página se puede acceder a la documentación y el código fuente.
2. Descomprimir la distribución
```
unzip apache-servicemix-6.0.0.zip
```
3. Ajustar el entorno editando el fichero bin/setenv (Linux) | bin\setenv.bat (Windows)
* Configurar variable para encriptación de propiedades
>Linux:
```
export APBA_JASYPT_ENCRYPTION_PASSWORD=14gr_48ABp
```
>Windows:
```
SET APBA_JASYPT_ENCRYPTION_PASSWORD=14gr_48ABp
```
* Ajustar la variable JAVA_HOME con la localización de una distribución Java 7 soportada por Apache ServiceMix 6.0.0
4. Configurar el conector SSL del componente Jetty de ServiceMix
* Crear una clave para el servidor en la carpeta etc:
```
keytool -genkeypair \
    -alias localhost \
    -keyalg RSA \
    -keysize 2048 \
    -dname "CN=localhost, OU=ADT, O=APBA" \
    -keypass test1234 \
    -validity 3650 \
    -storetype PKCS12 \
    -keystore jetty.p12 \
    -storepass test1234
```
* Arrancar ServiceMix 
>Linux:
```
bin/servicemix
```
>Windows:
```
bin\servicemix.bat
```
* Ejecutar los siguientes comandos en la consola Karaf:
```
config:edit org.ops4j.pax.web 
config:property-set org.ops4j.pax.web.config.file etc/jetty.xml
config:property-set javax.servlet.context.tempdir data/pax-web-jsp
config:property-set org.osgi.service.http.enabled false
config:property-set org.osgi.service.http.port 8181
config:property-set org.ops4j.pax.web.session.cookie.httpOnly true
config:property-set org.osgi.service.http.secure.enabled true
config:property-set org.osgi.service.http.port.secure 8143
config:property-set org.ops4j.pax.web.ssl.keystore etc/jetty.p12
config:property-set org.ops4j.pax.web.ssl.keystore.type PKCS12
config:property-set org.ops4j.pax.web.ssl.password test1234
config:property-set org.ops4j.pax.web.ssl.keypassword test1234
config:update
```
5. Instalar [Hawtio](https://hawt.io)
* Ejecutar los siguientes comandos en la consola Karaf:
```
feature:repo-add hawtio 1.4.60
feature:install hawtio-core
```
* Una vez hecho esto, se puede acceder a la [consola](https://localhost:8143/hawtio) (el usuario por defecto es smx, con la clave smx).
6. Instalar el [reino de autenticación/autorización JAAS de la APBA](https://github.com/adtapba/apba-realm)
* Ejecutar el siguiente comando en la consola Karaf:
```
bundle:install -s mvn:es.apba.infra.esb.support/apba-realm/1.0.0
```
7. Se puede parar ServiceMix ejecutando el siguiente comando en la consola Karaf:
```
system:shutdown
```
