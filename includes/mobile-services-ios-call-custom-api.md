
### Llamada a API personalizada desde una aplicación de iOS

Para llamar a esta API personalizada desde un cliente de iOS, use el método `MSClient invokeAPI`. Hay dos versiones de este método: una para las solicitudes con formato JSON y otra para cualquier tipo de datos:

	/// Invokes a user-defined API of the Mobile Service.  The HTTP request and
	/// response content will be treated as JSON.
	-(void)invokeAPI:(NSString *)APIName
	            body:(id)body
	      HTTPMethod:(NSString *)method
	      parameters:(NSDictionary *)parameters
	         headers:(NSDictionary *)headers
	      completion:(MSAPIBlock)completion;

	/// Invokes a user-defined API of the Mobile Service.  The HTTP request and
	/// response content can be of any media type.
	-(void)invokeAPI:(NSString *)APIName
	            data:(NSData *)data
	      HTTPMethod:(NSString *)method
	      parameters:(NSDictionary *)parameters
	         headers:(NSDictionary *)headers
	      completion:(MSAPIDataBlock)completion;


Por ejemplo, para enviar una solicitud JSON a una API personalizada denominada "sendEmail", pase un `NSDictionary` para los parámetros de solicitud:

	NSDictionary *emailHeader = @{ @"to": @"email.com", @"subject" : @"value" };

	[self.client invokeAPI:@"sendEmail"
	     body:emailBody
	     HTTPMethod:@"POST"
	     parameters:emailHeader
	     headers:nil
	     completion:completion ];
	    
Si necesita los datos devueltos, puede usar algo como esto:

	[self.client invokeAPI:apiName
                 body:yourBody
           HTTPMethod:httpMethod
           parameters:parameters
              headers:headers
           completion:  ^(NSData *result,
                          NSHTTPURLResponse *response,
                          NSError *error){
               // error is nil if no error occured
               if(error) { 
                   NSLog(@"ERROR %@", error);
               } else {
                   
		// do something with the result
               }
               
               
           }];

		

<!---HONumber=AcomDC_0204_2016-->