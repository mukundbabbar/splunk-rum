# Splunk Real User Monitoring - OpenTelemetry

## Inject splunk-otel-web.js agent using chrome plugin for testing the agent/correlation locally

## Install Injector Chrome extension (Note - Injector plugin is one of the many available extensions available to test the snippet on your laptop)

https://chrome.google.com/webstore/detail/injector/bfdonckegflhbiamlmidciapolfccmmb?hl=en

<img width="707" alt="Screen Shot 2022-09-05 at 9 06 32 pm" src="https://user-images.githubusercontent.com/5012739/188435400-d3c520b2-e90a-4572-81b9-9c9658a9efee.png">

## Create new Snippet

Provide a domain name to instrument with RUM agent

<img width="595" alt="Screen Shot 2022-09-05 at 9 12 39 pm" src="https://user-images.githubusercontent.com/5012739/188436039-132750da-ae1a-4983-a448-68bfffa98501.png">

### Copy the below snippet and update following variables - 

${RUM_ACCESS_KEY}

${RUM_APP_NAME}

${RUM_ENV_NAME}

${SPLUNK_REALM}

The snippet also includes example of adding custom attributes and sending events

```
console.log("Injecting")
var js = document.createElement("script");
js.type = "text/javascript";
js.src = "https://cdn.signalfx.com/o11y-gdi-rum/latest/splunk-otel-web.js";

js.onload = function() {
 SplunkRum.init({
        beaconUrl: "https://rum-ingest.${SPLUNK_REALM}.signalfx.com/v1/rum",
        rumAuth: "${RUM_ACCESS_KEY}",
        app: "${RUM_APP_NAME}",
        environment: "${RUM_ENV_NAME}",
        globalAttributes: {
          'enduser.type': 'platinum',
          'app.release': 'v1',
          'url.queryParams': window.location.search.substr(1),
          'enduser.id': 'ID_5600928'
        },
        });
        
        //Add error event
        const Provider = SplunkRum.provider;
        var tracer=Provider.getTracer('appModuleLoader');
        span = tracer.startSpan('custom_workflow', {
        attributes: {
            'workflow.name': 'doWork',
            'payment_id': '00088770000'
        }
        });
        doWork();
        span.end();
	
	function doWork(){
			for (let i = 0; i <= Math.floor(Math.random() * 400000); i += 1) {
					// empty
			}
			span.setAttribute('error', true);
			span.addEvent('task returned error in processing record');
	}
        
 console.log("onLoad")
};
document.body.appendChild(js);
console.log("Done injecting");
```

## Validate data in Splunk Observability Cloud and correlation with backend application traces

<img width="1223" alt="Screen Shot 2022-09-05 at 9 25 16 pm" src="https://user-images.githubusercontent.com/5012739/188438100-0a360b5a-d96d-4828-a00b-b488bee9074e.png">


