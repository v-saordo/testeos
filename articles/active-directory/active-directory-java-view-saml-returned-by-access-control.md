<properties
    pageTitle="Visualización del SAML que devuelve el servicio de control de acceso (Java)"
    description="Obtenga información acerca de cómo ver SAML devuelto por el servicio de Control de acceso en las aplicaciones de Java hospedadas en Azure."
	services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="01/09/2016" 
    ms.author="robmcm" />

# Visualización del SAML que devuelve el servicio de control de acceso de Azure

En esta guía se explica cómo ver el lenguaje de marcado de aserción de seguridad (SAML) subyacente devuelto a la aplicación mediante el servicio de control de acceso de Azure (ACS). La guía se elabora a partir del tema [Autenticación de usuarios web con el servicio de control de acceso de Azure mediante Eclipse][], proporcionando código que muestra la información de SAML. La aplicación completa tendrá un aspecto similar al siguiente.

![Salida de SAML de ejemplo][saml_output]

Para obtener más información sobre ACS, consulte la sección [Pasos siguientes](#next_steps).

> [AZURE.NOTE]El filtro de control de los servicios de acceso de Azure es una Community Technology Preview. Como versión preliminar de software, no cuenta formalmente con el respaldo de Microsoft.

## Requisitos previos

A fin de llevar a cabo las tareas de esta guía, complete el ejemplo de [Autenticación de usuarios web con el servicio de control de acceso de Azure mediante Eclipse][] y úselo como punto de partida de este tutorial.

## Incorporación de la biblioteca JspWriter a la ruta de acceso de compilación y el ensamblado de implementación

Agregue la biblioteca que contiene la clase **javax.servlet.jsp.JspWriter** a la ruta de acceso de la compilación y el ensamblado de implementación. Si usa Tomcat, la biblioteca es **jsp-api.jar** y se ubica en la carpeta **lib** de Apache.

1. En el explorador de proyectos de Eclipse, haga clic con el botón secundario en **MyACSHelloWorld**, haga clic en **Ruta de acceso de compilación**, después en **Configurar ruta de acceso de compilación**, a continuación, en la pestaña **Bibliotecas** y, por último, en **Agregar JAR internos**.
2. En el cuadro de diálogo **Selección de JAR**, navegue hasta el JAR necesario, selecciónelo y, a continuación, haga clic en **Abrir**.
3. Con el cuadro de diálogo **Propiedades de MyACSHelloWorld** abierto, haga clic en **Ensamblaje de implementación**.
4. En el cuadro de diálogo **Ensamblaje de implementación web**, haga clic en **Agregar**.
5. En el cuadro de diálogo **Nueva directiva de ensamblaje**, haga clic en **Entradas de ruta de acceso de compilación de Java** y haga clic en **Siguiente**.
6. Seleccione la biblioteca pertinente y haga clic en **Finalizar**.
7. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades de MyACSHelloWorld**.

## Modificación del archivo JSP para que muestre el SAML

Modifique **index.jsp** para que use el código siguiente.

	<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8"%>
	    <%@ page import="javax.xml.parsers.*"
	             import="javax.xml.transform.*"
	             import="org.w3c.dom.*"
	             import="java.io.*"
	             import="javax.xml.transform.stream.*"
	             import="javax.xml.transform.dom.*"
	             import="javax.xml.xpath.*"
	             import="javax.servlet.jsp.JspWriter" %>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
	<title>Sample ACS Filter</title>
	</head>
	<body>
		<h3>SAML information from sample ACS program</h3>
		<%!
	    void displaySAMLInfo(Node node, String parent, JspWriter out)
	    {
	    
		    try
		    {
				String nodeName;
			    int nChild, i;
			    
			    nodeName = node.getNodeName();
			    out.println("<br>");
			    out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
			       
			       // Attributes.
			       NamedNodeMap attribsMap = node.getAttributes();
			       if (null != attribsMap)
			       {
	                     for (i=0; i < attribsMap.getLength(); i++)
	                     {
	                            Node attrib = attribsMap.item(i);
	                            out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
	                     }
			       }
			       
			       // Child nodes.
			       NodeList list = node.getChildNodes();
			       if (null != list)
	 		       {
			              nChild = list.getLength();
			              if (nChild > 0)
			              {                    
	
				                 // If it is a text node, just print the text.
				                 if (list.item(0).getNodeName() == "#text")
				                 {
	                                 out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
				                 }
				                 else
				                 {
				                	 // Print out the child node names.
				                	 out.print("Contains " + nChild + " child node(s): ");   
		   		                     for (i=0; i < nChild; i++)
				                     {
					                    Node temp = list.item(i);
					                    
					                    out.print("<b>" + temp.getNodeName() + "</b>");
					                    if (i < nChild - 1)
					                    {
					                    	// Separate the names.
					                    	out.print(", ");
					                    }
					                    else
					                    {
					                    	// Finish the sentence.
					                    	out.print(".");
					                    }
					                    	
				                     }
					                 out.println("<br>");
					                 
					                 // Process the child nodes.
					                 for (i=0; i < nChild; i++)
				                     {
					                    Node temp = list.item(i);
					                    displaySAMLInfo(temp, parent + nodeName + "\", out);
				                     }
				               }
			              }
			          }
			      }
			    catch (Exception e)
			    {
			    	System.out.println("Exception encountered.");
			    	e.printStackTrace();	    	
			    }
		    }
	    %>
	
	    <%
	    try 
	    {
		    String data  = (String) request.getAttribute("ACSSAML");
		    
		    DocumentBuilder docBuilder;
			Document doc = null;
			DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
			docBuilderFactory.setIgnoringElementContentWhitespace(true);
			docBuilder = docBuilderFactory.newDocumentBuilder();
			byte[] xmlDATA = data.getBytes();
			
			ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
			doc = docBuilder.parse(in);
			doc.getDocumentElement().normalize();
			
			// Iterate the child nodes of the doc.
	        NodeList list = doc.getChildNodes();
	
	        for (int i=0; i < list.getLength(); i++)
	        {
	        	displaySAMLInfo(list.item(i), "", out);
	        }
		}
	    catch (Exception e) 
	    {
	    	out.println("Exception encountered.");
	    	e.printStackTrace();
		}
	    
	    %>
	</body>
	</html>

## Ejecución de la aplicación

1. Ejecute la aplicación en el emulador del equipo o realice la implementación en Azure; para ello, siga los pasos descritos en [Autenticación de usuarios web con el servicio de control de acceso de Azure mediante Eclipse][].
2. Inicie un explorador y abra la aplicación web. Después de iniciar sesión en la aplicación, verá la información de SAML, incluida la aserción de seguridad proporcionada por el proveedor de identidades.

## Pasos siguientes

Para seguir explorando la funcionalidad de ACS y experimentar con escenarios más sofisticados, consulte [Access Control Service 2.0][].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Autenticación de usuarios web con el servicio de control de acceso de Azure mediante Eclipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
 

<!---HONumber=AcomDC_0114_2016-->