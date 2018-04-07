Hi all, Liferay 7 by default uses the legacy XML libraries knowed as xml-api, xerces and xalan. Since many years they are included inside the JRE. 
This legacy libraries included in tomcat 8 are cause of bugs inside Liferay. I share the steps how remove them, so you can use directly the native XML libraries inside the JRE.

1- Open the setenv.sh file inside ${home.liferay}/tomcat-8.0.32/bin and add the following properties in the CATALINA_OPTS: 

-Djavax.xml.validation.SchemaFactory:http://www.w3.org/2001/XMLSchema=com.sun.org.apache.xerces.internal.jaxp.validation.XMLSchemaFactory -Djavax.xml.parsers.DocumentBuilderFactory=com.sun.org.apache.xerces.internal.jaxp.DocumentBuilderFactoryImpl -Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl



2- Liferay DXP 7.0 has a bad error because it cablate the xerces library in the services code. So you need a patch to add in the ${home.liferay}/tomcat-8.0.32/webapps/ROOT/WEB-INF/lib folder. The patch can be created with a simple maven project adding the following two classes in the src/main/java:



org.apache.xerces.parsers.SAXParser:



package org.apache.xerces.parsers;



public class SAXParser extends com.sun.org.apache.xerces.internal.parsers.SAXParser {



}



org.apache.xerces.xni.XNIException:



package org.apache.xerces.xni;



/**

 * This exception is the base exception of all XNI exceptions. It

 * can be constructed with an error message or used to wrap another

 * exception object.

 * <p>

 * <strong>Note:</strong> By extending the Java

 * <code>RuntimeException</code>, XNI handlers and components are

 * not required to catch XNI exceptions but may explicitly catch

 * them, if so desired.

 *

 * @author Andy Clark, IBM

 *

 * @version $Id: XNIException.java,v 1.6 2010-11-01 04:40:19 joehw Exp $

 */

public class XNIException

    extends RuntimeException {



    /** Serialization version. */

    static final long serialVersionUID = 9019819772686063775L;



    //

    // Data

    //



    /** The wrapped exception. */

    private Exception fException;



    //

    // Constructors

    //



    /**

     * Constructs an XNI exception with a message.

     *

     * @param message The exception message.

     */

    public XNIException(String message) {

        super(message);

    } // <init>(String)



    /**

     * Constructs an XNI exception with a wrapped exception.

     *

     * @param exception The wrapped exception.

     */

    public XNIException(Exception exception) {

        super(exception.getMessage());

        fException = exception;

    } // <init>(Exception)



    /**

     * Constructs an XNI exception with a message and wrapped exception.

     *

     * @param message The exception message.

     * @param exception The wrapped exception.

     */

    public XNIException(String message, Exception exception) {

        super(message);

        fException = exception;

    } // <init>(Exception,String)



    //

    // Public methods

    //



    /** Returns the wrapped exception. */

    public Exception getException() {

        return fException;

    } // getException():Exception



    public Throwable getCause() {

       return fException;

    }

} // class QName

   
and a simple pom.xml:

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">



	<modelVersion>4.0.0</modelVersion>

	<artifactId>native-xml-patch</artifactId>

	<packaging>jar</packaging>



</project>



3- build the patch through the command: mvn install and copy in the lib tomcat folder



4- restart liferay



You are ready to work with the native XML libraries. Good work!
