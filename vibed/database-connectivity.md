# Conectividad a bases de datos

Vibe.d hace fácil el acceso a bases de datos en los servidores (*backend
services*). El soporte para el acceso a bases de datos **MongoDB** y **Redis**
es directo con vibe.d, aunque existen más adaptadores para otras bases de
datos en [code.dlang.org](https://code.dlang.org).

### MongoDB

El acceso a bases de datos MongoDB se modela alrededor de la clase
[`MongoClient`](http://vibed.org/api/vibe.db.mongo.client/MongoClient).
Esta implementación no tiene dependencias externas y se implementa usando
los *sockets* asíncronos de vibe.d, con lo que no hay bloqueo en caso de
que la conexión tenga alguna latencia.

    auto client = connectMongoDB("127.0.0.1");
    auto users = client.getCollection("users");
    users.insert(Bson("peter"));

### Redis

El soporte de bases de datos Redis se implementa también mediante los *sockets*
de vibe.d y tampoco tiene dependencias externas. La clase central de esta
implementación es [`RedisDatabase`](http://vibed.org/api/vibe.db.redis.redis/RedisDatabase),
que permite ejecutar comandos contra un servidor de Redis. Además están
disponibles clases y funciones específicas, como [`RedisList`](http://vibed.org/api/vibe.db.redis.types/RedisList),
que permite acceder de forma transparente a listas guardadas en Redis.

### MySQL

El soporte para bases de datos MySQL sin dependencias externas contra la
biblioteca oficial de MySQL se consigue gracias al proyecto
[mysql-native](http://code.dlang.org/packages/mysql-native). También soporta
conexiones no bloquenates mediante *sockets* de vibe.d.

### Postgresql

Un completo cliente para bases de datos PostgreSQL se implementa mediante el
módulo externo [dpq2](http://code.dlang.org/packages/dpq2), módulo basado en
la biblioteca oficial `libpq`. También usa el sistema de eventos de vibe.d
para implementar el comportamiento asíncrono.

Otra alternativa para bases de datos PostgreSQL es [ddb](http://code.dlang.org/packages/ddb),
que implementa un cliente a esta base de datos usando *sockets* de vibe.d sin
dependencias externas.
