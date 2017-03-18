## producing-web-service
一、开发

1、
As gradle does not have a JAXB plugin (yet), it involves an ant task, which makes it a bit more complex than in maven.

In both cases, the JAXB domain object generation process has been wired into the build tool’s lifecycle so there are no extra steps to run.

2、In all of these chunks of code, the **com.elasticjee.start** classes will report compile-time errors in your IDE unless you have run the task to generate the domain classes based on the WSDL.

3、Spring WS uses a different servlet type for handling SOAP messages: MessageDispatcherServlet. It is important to inject and set ApplicationContext to MessageDispatcherServlet. Without that, Spring WS will not detect Spring beans automatically.
  By naming this bean messageDispatcherServlet, it does not replace Spring Boot’s default DispatcherServlet bean.
  DefaultMethodEndpointAdapter configures annotation driven Spring WS programming model. This makes it possible to use the various annotations like @Endpoint mentioned earlier.
  DefaultWsdl11Definition exposes a standard WSDL 1.1 using XsdSchema
  
4、It’s important to notice that you need to specify bean names for MessageDispatcherServlet and DefaultWsdl11Definition. Bean names determine the URL under which web service and the generated WSDL file is available. In this case, the WSDL will be available under http://<host>:<port>/ws/countries.wsdl.
  
  This configuration also uses the WSDL location servlet transformation servlet.setTransformWsdlLocations(true). If you visit http://localhost:8080/ws/countries.wsdl, the soap:address will have the proper address. If you instead visit the WSDL from the public facing IP address assigned to your machine, you will see that address instead.
  
二、测试

1、Now that the application is running, you can test it. Create a file request.xml containing the following SOAP request:
```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
				  xmlns:gs="http://elasticjee.com/start/start-producing-web-service">
   <soapenv:Header/>
   <soapenv:Body>
      <gs:getCountryRequest>
         <gs:name>Spain</gs:name>
      </gs:getCountryRequest>
   </soapenv:Body>
</soapenv:Envelope>
```

The are a few options when it comes to testing the SOAP interface. You can use something like SoapUI or just use command line tools if you are on a *nix/Mac system as shown below.
```shell
$ curl --header "content-type: text/xml" -d @request.xml http://localhost:8080/ws
```

As a result you should see this response:
```xml
<?xml version="1.0"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/">
  <SOAP-ENV:Header/>
  <SOAP-ENV:Body>
    <ns2:getCountryResponse xmlns:ns2="http://elasticjee.com/start/start-producing-web-service">
      <ns2:country>
        <ns2:name>Spain</ns2:name>
        <ns2:population>46704314</ns2:population>
        <ns2:capital>Madrid</ns2:capital>
        <ns2:currency>EUR</ns2:currency>
      </ns2:country>
    </ns2:getCountryResponse>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```