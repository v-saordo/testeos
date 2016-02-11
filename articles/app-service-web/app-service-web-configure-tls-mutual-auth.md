<properties 
	pageTitle="Configuración de la autenticación mutua de TLS para una aplicación web" 
	description="Aprenda a configurar la aplicación web para que use la autenticación de certificado de cliente en TLS." 
	services="app-service" 
	documentationCenter="" 
	authors="naziml" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/17/2015" 
	ms.author="naziml"/>

# Configuración de la autenticación mutua de TLS para una aplicación web

## Información general ##
Puede restringir el acceso a la aplicación web de Azure habilitando para este diferentes tipos de autenticación. Una manera de hacerlo es autenticar con un certificado de cliente cuando la solicitud sea a través de TLS/SSL. Este mecanismo se denomina autenticación mutua de TLS o autenticación de certificado de cliente, y en este artículo se detalla cómo configurar la aplicación web para que use la autenticación de certificado de cliente.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Configuración de la aplicación web para la autenticación de certificado de cliente ##
Para configurar la aplicación web para que exija certificados de cliente, debe agregar la configuración del sitio clientCertEnabled para la aplicación web y establecerla como verdadera. Esta configuración no está actualmente disponible a través de la experiencia de administración en el portal, y a tal efecto deberá usarse la API de REST.

Puede usar la [herramienta ARMClient](https://github.com/projectkudu/ARMClient) para simplificar la creación de la llamada a la API de REST. Después de iniciar sesión con la herramienta, deberá emitir el siguiente comando:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose
    
y reemplazar todo lo que aparezca entre {} por la información de la aplicación web, y crear un archivo llamado enableclientcert.json con el siguiente contenido JSON:

> { "location": "My Web App Location", "properties": { "clientCertEnabled": true } }


Asegúrese de cambiar el valor de "location" por aquel en el que se encuentra su aplicación web, por ejemplo, Centro y norte de EE. UU. o Oeste de EE. UU., etc..


## Acceso al certificado de cliente desde la aplicación web ##
Cuando la aplicación web esté configurada para usar la autenticación de certificado de cliente, el certificado de cliente estará disponible en la aplicación mediante un valor codificado en base64 en el encabezado de solicitud "X-ARR-ClientCert". La aplicación puede crear un certificado a partir de este valor y luego usarlo con fines de autenticación y autorización en la aplicación.

## Consideraciones especiales sobre la validación de certificados ##
El certificado de cliente que se envía a la aplicación no pasa ninguna validación de la plataforma de aplicaciones web de Azure. La validación de este certificado es la responsabilidad de la aplicación web. Este es el código de ASP.NET de ejemplo que valida las propiedades del certificado para la autenticación.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;
                
                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 && DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;
                
                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }

<!---HONumber=AcomDC_0107_2016-->