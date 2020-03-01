# REST API

REpresentational State Transfer

REST - a way of providing interoperability between computer systems on the internet. 

Components of REST

* client-server
* stateless
* cache
* uniform interface
* layered system
* code-on-demand (optional)

Self-descriptive message
```
HTTP/1.1 200 OK
Content-Type: application/json-patch+json
[ { "op" : "remove", "path" : "a/b/c"} ]
```

