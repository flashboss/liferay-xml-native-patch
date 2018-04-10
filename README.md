Hi all, Liferay 7.0 DXP SP7 by default uses the legacy XML libraries known as xml-api, xerces and xalan. Since many years they are included inside the JRE. 
This legacy libraries included in tomcat 8 are cause of bugs inside Liferay. I share the steps how remove them, so you can use directly the native XML libraries inside the JRE.

1- Open the setenv.sh file inside ${home.liferay}/tomcat-8.0.32/bin and add the following properties in the CATALINA_OPTS: 
-Djavax.xml.validation.SchemaFactory:http://www.w3.org/2001/XMLSchema=com.sun.org.apache.xerces.internal.jaxp.validation.XMLSchemaFactory -Djavax.xml.parsers.DocumentBuilderFactory=com.sun.org.apache.xerces.internal.jaxp.DocumentBuilderFactoryImpl -Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl

2- Liferay DXP 7.0 has a bad error because it cablates the old xerces libraries in the services code. So you need a patch to add in the ${home.liferay}/tomcat-8.0.32/webapps/ROOT/WEB-INF/lib folder. The patch can be can be found here

3- build the patch through the command: mvn install and copy in the lib tomcat folder

4- XML libraries need the org.w3c.dom package working as system library. To add it and avoid unespected ClassNotFound exceptions put this property in the portal-ext.properties of Liferay:

module.framework.properties.org.osgi.framework.bootdelegation=com.liferay.aspectj,__redirected,  com.sun.jna,com.sun.ccpp,com.liferay.portal.servlet.delegate, com.sun.syndication,org.w3c.dom,sun.reflect

5- restart liferay

You are ready to work with the native XML libraries. Good work!
