# Fermat P2P Network V1 Development Bounty

### Introduction
The main objective of this bounty is to create a decentralized P2P network between the Fermat Nodes. In this first approach we'll create the first version of a Network Node and the first version of the Client Node. The current implementation called Cloud Client and Cloud Server are going to be used as a reference to extrack whatever is usefull, and that techology is going still be supported as an alternative communication method.

The Network Node will have all the features needed to the interaction with its peers.

### Glosary

* Fermat Network: Fermat peer-to-peer network.
* Network Node: Fermat Network decentralized server.
* Network Client: Fermat Network decentralized Client.
* Seed Server: Fermat First Network Node. Its the first reference for all the new nodes. There could be more that one seed server. Seed server's IP addess and port are known by clients at compile time.
* Network Relationships:
  * Node-Node
  * Client-Node
  * Client-Client
* Network Node Catalog: Distributed catalog of nodes. It is used for the discovering and searching of Nodes. It will be managed by Nodes.
* Node Profile: Profile of each node, it'll contains the necessary information to communicate with it: ip, port, name, etc.
* Actor Catalog: Distributed catalog of Actors. Is used for the discovering and searching of  Actors. It will be managed by Nodes.
* Network Node Channel: Communication Channel between Nodes.
* Network Client Channel: Communication Channel between Node and Client & Vice-versa.
* Network Call Channel: Communication Channel between Client and Client.

### Scope
La **"Fermat Network"** sera la red *"peer-to-peer"* utiliza por el sistema Fermat, y estara compuesta por dos tipos de componentes, los nodos llamados **"Network Nodes"** y los clientes llamados  **"Network Clients"** los cuales interactuan entre si, estas interacciones podrán ser de la siguientes formas **"Node to Node"**, **"Node to Client"** y **"Client to Client"**. 

Los **"Network Nodes"** serán los componentes responsables de mantener la intercomunicación entre la red *"peer-to-peer"*, estos fungirán como servidores descentralizados los cuales proporcionaran servicios especializados a cada uno de los clientes y también a otros nodos, también tiene la responsabilidad de presentar y dar a conocer a cada uno de estos clientes en la red.

Uno de los problemas principales en las redes *"peer to peer"* es el descubrimiento de los nodos disponibles en la red, para esto se implementara un Catalogo distribuido de nodos **"Network Node Catalog"** que tiene como finalidad mantener la relación e existencia de cada uno de los nodos disponibles que conforman la red, en dicho catalogo se almacenara información básica de cada uno de los nodos conocida como el **"NodeProfile"**, el cual estará compuesto por datos como: ip, puerto, nombre, localización y etc.  La responsabilidad de la administración y mantenimiento de este catalogo sera exclusiva de los mismos nodos **"Network Node"**, estos tendrán la responsabilidad de llenar, compartir y mantener actualizado este catalogo.

También se contara con la implantación de un catalogo distribuido de actores **"Actor Catalog"** en cual sera utilizado para el descubrimiento y busqueda de cada uno de ellos en la red "peer-to-peer", como en el caso del **"Network Node Catalog"**  la administración y mantenimiento de este catalogo sera exclusiva responsabilidad de los mismos nodos **"Network Node"**.

Se ha definido que cada  **"Network Node"** estará compuesto por dos canales principales de comunicación conocidos **"Network Node Channel"** y **"Network Client Channel"**, cada uno especializado para el intercambio de datso entre los componentes de la red dependiendo su tipo; es decir tendrán un canal de comunicación exclusiva para el envió de información entre  **"Network Node"** a **"Network Node"** , y otro canal utilizado para el envió de información entre **"Network Clients"** a **"Network Node"** y viceversa.

El cliente **"Network Client"** sera el que se ejecutara en cada uno de los dispositivos que fungirán como clientes de la red peer-to-peer, y son los responsables de proveer una interface o API de comunicación a los otros componentes del sistema tales como son los **"Network Services"** y **"Actores"**. 

Los clientes contaran también con dos canales de comunicación exclusivos llamados **"Network Client Channel"** y **"Network Call Channel"**, el primero para la intereacción con los nodos de la red, y otro que sera utilizando para envió de información y datos entre clientes de la red.


Para cada uno de estos canales se han identificado los siguientes casos de usos y métodos, los cuales serán expuestos como servicios por cada uno de estos ellos:

### Fermat Network Nodes:
#### Network Node Channel:

1. AddNodeToCatalog   (NodeProfile)
2. UpdateNodeInCatalog   (NodeProfile)
3. GetCatalog    (Records, Offset)
4. GetCatalogTxs  (Records, Timestamp)
5. ReceiveNodeCatalogTransactions (NodeCatalogTxs)
6. ReceiveActorCatalogTransactions (ActorCatalogTxs)
#### Network Client Channel:

1. GetNearbyNodes (Location)
2. CheckIn(ClientProfile), 
3. CeckIn(NetworkServiceProfile) 
4. CheckIn(ActorProfile)
5. CheckOut(ClientProfile), 
6. CeckOut(NetworkServiceProfile) 
7. CheckOut(ActorProfile)
8. CheckedInProfileDiscoveryQuery  (DiscoveryQueryParams)
9. ActorProfileTraceDiscoveryQuery  (DiscoveryQueryParams)     
10. ChangeClientProfileHome (Profile, HomeNodeProfile)
11. NetworkServiceCall (FromNetworkService, ToNetworkService)   
12. ActorCall (FromActor, ToActor, FromNetworkService)

### Fermat Network Clients:
#### Network Client Channel:

1. GetNearbyNodes (Location)
2. RegisterProfile (Profile, ParentProfile)  
3. UnRegisterProfile (Profile)
4. RegisteredProfileDiscoveryQuery  (DiscoveryQueryParams)
5. ActorTraceDiscoveryQuery  (DiscoveryQueryParams)     
6. ChangeProfileHome (Profile, HomeNodeProfile)
7. NetworkServiceCall (FromNetworkService, ToNetworkService)   
8. ActorCall (FromActor, ToActor, FromNetworkService)
#### Network Call Channel:

1. ConnetToVpnChannel()
2. SendMessage()

### Tasks

Having in count the responsibility that must be accomplished by the **"Network Nodes"** of distributing and mantain the actor and node catalogs we define a serie of tasks, to be done by agent components:

1. Propagate Node Catalog Task
2. Propagate Actor Catalog Task

### Network Node Features

#### Task: Propagate Node Catalog 
Esto es un agente que se ejecuta cada 1 minuto en el Network Node. Al iniciar dicho agente el Network Node Verifica en la tabla de "NODES_CATALOG_TRANSACTIONS_PENDING_FOR_PROPAGATION" si existen registros nuevos, en caso de que existan registros el Network Node busca en el catalogo de Network Nodes 10 Network Nodes que tengan menos notificaciones tardías de registros entonces obtiene un Network Node de la lista e intenta conectarse con el Network Node si la conexión es satisfactoria se envía el bloque de transacciones, recibe una respuesta de parte del otro Network Node Verifica si es una "Notificación de información tardia" entonces Aumenta el valor del campo "LATE_NOTIFICATION_COUNTER" para ese Network Node Actualiza el registro en la base de datos y cierra la conexión con dicho Network Node, aumenta el contador de propagaciones exitosas, en caso contrario de que no se logro conectarse obtiene otra vez un Network Node de la lista e intenta conectarse con el Network Node para enviarle el bloque de transacciones; luego de haber recorrido los 10 Network Nodes que tienen menos notificaciones tardías de registros el catalogo de Network Nodes valida si el contador de propagaciones exitosas es igual a 10 entonces borra las transacciones enviadas a los otros Network Nodes de la tabla "NODE_CATALOG_TRANSACTIONS_PENDING_FOR_PROPAGATION" y se duerme el agente por el tiempo configurado, en caso de que el contador de propagaciones exitosas es diferente a 10 entonces busca en el catalogo de Network Nodes 10 que tengan menos notificaciones tardías de registros y repite toda la operación nuevamente.

#### Task: Propagate Actor Catalog 
This scheduled task es el encargado de realizar el proceso de propagación entre los nodos la información almacenada en el catalogo de actores, comienza verificando en la tabla de "ACTOR_CATALOG_TRANSACTIONS_PENDING_FOR_PROPAGATION" si existen registros, cuando consigue estos registro el mismo agente va conectarse con otros "Network Nodes" y le transmitirá el conjunto de transacciones en un bloque. Cada vez que logre transmitir el bloque exitosamente a otro "Network Nodes" se ira descontando del contador de "Propagaciones exitosas", este al confirmar que el contador ha alcanzado el numero de cero (0) procederá a borrar todos los registros que se encontraban almacenados en la tabla "ACTOR_CATALOG_TRANSACTIONS_PENDING_FOR_PROPAGATION".

#### Database:

![network node data base](https://cloud.githubusercontent.com/assets/12099493/11034740/0d57f5ea-86c0-11e5-9bcb-b47911df4e26.png)

### Network Node Channel Features

#### Process: Receive Actor Catalog Transactions

Este proceso inicia cuando el Network Node recibe nuevo bloque de transacciones de parte de otro Network Node, luego resta uno al contardor de propagaciones pendientes, Obtiene una transacción del bloque y verifica si existe el registro en la tabla "ACTOR_CATALOG" si es así entonces toma otro bloque transacción y repite operación, en caso de que no exista ese registro en la tabla "ACTOR_CATALOG" entonces aplica la transaccion en la tabla "ACTOR_CATALOG" guarda las transacciones en la tabla "ACTORS_CATALOG_TRANSACTIONS" y verifica si la propagación ha alcanzado el número máximo de nodos si el Contador de propagaciones pendientes es diferente a 0 entonces guarda las transacciones en la tabla "PENDING_CATALOG_TRANSACTIONS_FOR_PROPAGATION"

#### Process: Get Catalog
Este proceso inicia cuando el "Network Node" recibe una nueva solicitud de catalogo de "Network Node", entonces Busca y filtra por el numero de registros solicitados en la tabla "NODES_CATALOG" y por ultimo Retorna dicho catalogo al solicitante otro "Network Node".

#### Process: Get Catalog Transactions
Este proceso inicia cuando el "Network Node" Recibe una nueva solicitud de transacciones catalogo de "Network Node", entonces busca y filtra por el numero de registros solicitados en la tabla "CATALOG_TRANSACTIONS" y por ultimo retorna dicho catalogo al solicitante otro "Network Node".

#### Process: Receive Node Catalog Transactions
En este proceso un "Network Node" recibe un nuevo bloque de transacciones, luego va Tomando una transacción del bloque para procesarla, por cada transacción recibida verifica si existe en la tabla "NODE_CATALOG", si existe registro entonces Aumenta el contador de notificación de aviso tardío en el caso de que no exista el registro entonces aplica la transacción en la tabla "NODE_CATALOG", Guarda las transacciones en la tabla "CATALOG_TRANSACTIONS" y por ultimo guarda las transacciones en la tabla "NODES_CATALOG_TRANSACTIONS_PENDING_FOR_PROPAGATION" y responde al "Network Node" que envió el bloque el número de registros ya conocidos.

#### Process: Client To Node Connection
Este proceso inicia cuando el Network Node recibe la nueva solicitud de conexión de parte de un Network Client si la conexión es satisfactoria entonces inserta en la base de datos "CLIENT_CONNECTION_HISTORY" un nuevo registro con el perfil del Network Node con estatus "SUCCESS" y realizan el Intercambio de claves publicas, si la conexión no es satisfactoria entonces inserta en la base de datos "CLIENT_CONNECTION_HISTORY" un nuevo registro con el perfil del Network Node con estatus "FAIL" y envía notificación de conexión fallida.

#### Process: Get Nearby Nodes
Este proceso inicia cuando el Network Node Recibe la nueva solicitud de lista de Network Nodes cercanos, entonces calcula la distancia de los Network Nodes registrados en la tabla "NODE_CATALOG" con relación a la geolocalización del Network Client ordena el resultado de los cálculos de menor a mayor, extrae los 50 primeros nodos mas cercanos y retorna la lista al Network Client.

### Network Client Features

#### Database: 
![client node data base](https://cloud.githubusercontent.com/assets/12099493/11034748/1972311a-86c0-11e5-9e4a-b698b36a0c5a.png)

## Other resources:

### Workflows:

https://prezi.com/ae3v-gqmxeuy/fermat-network/

### Live Meetings recorded:

https://plus.google.com/events/cm854k049ils0im37bunqf6j6pk
https://plus.google.com/events/ce2ot7dru6k4nn7grqnmt05ke4s
https://plus.google.com/events/caacvbclj1l3jkutpv4rqcc6mgk
https://plus.google.com/events/cqi7tt576hmg2clindlduqhjfjc
https://plus.google.com/events/cdmbbvvvah3gf6vqveaf67g8hs0

## Timeline

Based on current workload and resouces available, the delivery date of this bounty will be **April 2016**.

## Evaluation

To be considered success this bounty must pass the following tests:

* Management and distribution of Node Catalog:
  * Succesfully registration of Multiple Network Nodes to the Seed Server.
  * Proper updating of the Nodes Catalog when this happened.
  * Right propagation of the Nodes Catalog (7).
* Succesfully registration of Actors.
* Succesfully registration of Network Clients.
* Succesfully registration of Network Services.
* Management and distribution of Actors Catalog:
  * Succesfully registration of Multiple Actors to the Seed Server. LUIS:  ???? que seria esto ???
  * Proper updating of the Actors Catalog when this happened.
  * Right propagation of the Actors Catalog (7).
* Geo-location search (get nearby nodes).
* Network Calls between clients.
* Actor Calls between clients.
* Test migration of one network service to the new scheme (selected: ccp-actor-network-service-intra-actor).
  
*[Evaluation results to be completed after evaluation]*

## Limitations
* Calls analysis not completed.
* Database and File System add-ons linux version creation in progress.
* Location Add-on linux version not created.
* 


LUIS: No veo el tema del HOME Node de los clientes, desde su eleccion, su aceptacion por parte de un nodo, la mudanza de un home node a otro, etc. Fijense si ademas de eso no le falta mas nada a este doc.

