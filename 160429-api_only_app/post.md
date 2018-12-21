# API only application - Development, Feature and Possibility [BETA]

Web application, as we see it, comes in different flavours, specially if the implementation technologies are taken into consideration. One such flavour is API only application. In general when people talks about API only, they signify portability and flexibility, because there are many possibilities with API only application.

### In brief

API only application, in general serves as the backend of a service oriented application, where frontend can be anything, from a desktop client to the command interface of a robot. It’s an open gateway for communication. It can be considered as the bearer of Internet of Things (IoT) concept.

### A little bit deeper

If we focus on API only apps from a more technical perspective, it is closely related to inter-process communication (IPC). Another concept that get significance during this discussion is object serialization. When any two device communicates or multiple application on a single device communicates (it doesn’t matter whether it is inter-process or inter-device), a message transmission and reception protocol is required. If TCP/IP stack is considered as the backbone data bearer, and HTTP as the application layer protocol then we can transmit data flawlessly using another scheme, which is object serialization. There are different object serialization schemes including JavaScript Object Notation (JSON), Yet Another Markup Language (YAML), Extensible Markup Language (XML), D-Bus and many more (of course some of them are highly domain specific). These scheme comes to play when the necessity of complex data structure transmission over network arises. Technically this is resolved through an en/decoder on the both transmission edges.

### Applications

API only application can be used in a lot of different scenarios, including but not limited to

* Web application development
* Desktop/mobile client development
* Cross platform application development
* Command interface development
* Payment gateway development
* Streaming service development
* Presentation layer protocol development

### In the wild

You may look into the application backends below for getting a closer look into the API only apps.

#### Rails

https://github.com/spastorino/todo-rails-api-ember-backend

#### Django

https://github.com/23andMe/api-example-django

#### Node.js

https://github.com/mikedeboer/node-github
<br><br><br>
API only applications are not meant to be used by end users. It’s primary consumers are different applications that have the least capability of communicating with the web. Programming languages or development framework has never been the limitation for this kind of application, cause the requirements are too few and the inter-device/framework/language portability burden is negligible.

### References

TODO


