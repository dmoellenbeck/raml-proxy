# Generic RAML Proxy

The RAML Proxy is a *generic* component which can be injected at instantiation time with a suitable RAML file, which will then proxy the APIs defined in the RAML to the base-url of the underlying API.

It contains the following features:
* Consistent and extensive error handling
* Consistent error response format as per JSONAPI specific
* Correlation creation ID logic if none available
* Consistent logging to include CorrelationID in logs.
* Extra configurable APIs for the following:
  * `/console` - for the RAML console
  * `/` - for the heartbeat with standard JSONAPI response
  * `/policies` - to list all the policies which this runtime has available in the policies directory
  * `/logs` - to forward a request through to the underlying API Implementation to extract API's logs via an API Impl hosted WebApp.

There are many configurable aspects of this component which will override the defaults.  These can be controlled through the `-D-M<option>` on the runtime command line, or they can be controlled via the various configuration files within the Mule Gateway Runtime e.g. `config.properties` or even the `warpper.conf` file for the runtime.

The following table explains each configuration parameter:
## Configurable Parameters
Configurable parameters for this proxy are as follows:
* `PROXY_API_NAME`

   This is the name given to this API

   e.g. Payments
* `PROXY_API_VERSION`

   This is the version of the API used for logging and heartbeat responses

   e.g. v1
* `IMPLEMENTATION_HOST`

   The hostname of the API implementation which is being proxied.  This will be where the call is made to

   DEFAULT: localhost

   e.g. localhost
* `IMPLEMENTATION_PORT`

   This is the implementation port which the API implementation is listening on.

   DEFAULT: 80

   e.g. 8081
* `IMPLEMENTATION_PATH`

   This is the root base URL Path which the API can be found on the Host and Port.

   e.g. /api

* `IMPLEMENTATION_LOG_PATH`

  This is the root URL Path which the Proxy will forward calls to from the `/log` API on the Proxy.  This URL will inherit the same IMPLEMENTATION_HOST and IMPLEMENTATION_PORT as the main API Proxying.

    DEEAFUL: /logs

    e.g. /apilogs

* `CLIENT_ID`

   This is the Client ID of the API from the point of view of the Mulesoft platform

   e.g. id123456-98765-1234
* `CLIENT_SECRET`

  This is the API's Client Secret

  e.g. secret1234

* `CORRELATION_ID_NAME`

  This is the name of the header field which will hold the Correlation ID (sometimes called Transaction ID).  If the header field is not present in the request, then the Proxy will automatically create a unique ID and place it in the downstream request to the API implementation and in the Responses.

  DEFAULT: `x-transaction-id`

  e.g. x-correlation-id
* `CLIENT_ID_NAME`

   This is the name of the header field which holds the name of the Client ID to be extracted for logging.  This is customer specific but likely to default to `x-client-id`

   DEFAULT: `x-client-id`

   e.g. x-foobar-client-id
* `CLIENT_SECRET_NAME`

  This is the name of the header field which holds the name of the Client Secret.

  DEFAULT: `x-client-secret`

  e.g. x-foobar-client-secret
* `POLICIES_DIR`

  This is the directory under which the Proxy's Policy files can be found on the Runtime's filesystem.  This is used to return the list of policies and their contents, when the `/policies` URL is called on the proxy.

    DEFAULT: `/opt/mule/policies`
* `LOG_LEVEL`
  This is the Log4J Logging level which will be applied to the root logger for this Proxy.  It must be one of the Log4J2 ennumerations [ERROR | WARN | INFO | DEBUG | TRACE ]

    DEFAULT: `INFO`

* `LOG_PATTERN`
  This is the logging pattern which will be applied to the Log File (NOTE: Is not applied to the Console output) for this Proxy.  This must follow the Log4j2 pattern format.

  DEFAULT: `[%d] [${hostName}] [%thread] [%logger] [%-5p] message=%m%n`

  e.g. `[%d{yyyy-MM-dd HH:mm:ss.SSS}] [${hostName}] [%t] [%level] [%c] - %msg%n`
## Error Responses

   Error responses are per the JSONAPI format which can be found [here](http://jsonapi.org/format/#error-objects)

   An example:
   ```
{
  "errors": [
    {
      "id": "123456789-a",
      "status": "502",
      "code": "BAD_GATEWAY",
      "title": "Unable to connect to upstream service.",
      "source": {
        "pointer": "/api",
        "parameter": {}
      },
      "meta": {
        "dateTime": "2017-05-04T16:54:14+0100"
      }
    }
  ]
}```
