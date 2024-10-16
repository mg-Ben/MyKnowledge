---
tags:
  - Others
  - Elastic
  - Observability
---
# Core principles
_APM Stands for Application Performance Monitoring_
The APM solution consists of adding in [[Python]], [[Java]] or [[Go]] code (among others) a configuration (i.e. library) so as to monitor that application.
## APM Agent
Measures application performance and tracks errors.
### Example in Python - Flask
An [[#APM Agent]] in Python (Flask) might look like this:
```Python
from elasticapm.contrib.flask import ElasticAPM

app.config['ELASTIC_APM'] = {
    'SERVICE_NAME': '<SERVICE-NAME>',
    'SECRET_TOKEN': '<SECRET-TOKEN>',
}
apm = ElasticAPM(app)
```
Once you have configured the agent, it will automatically track transactions and capture uncaught exceptions within Flask. However, you can also capture an arbitrary exception with:
```Python
try:
    1 / 0
except ZeroDivisionError:
    apm.capture_exception()
```
Or log a generic message thorugh:
```Python
apm.capture_message('hello, world!')
```
## APM Server
Receives data from [[#APM Agent]] and stores it in [[ElasticSearch]] as [[ElasticSearch#Document|documents]].
### Explore data
You can explore [[#APM Agent|APM Agents]] logs or captured events from [[Kibana]]. To do this, go to hamburger menu _**☰** > Observability > APM_.