# microservice-in-golang
based on the tutorial https://ewanvalentine.io/microservices-in-golang-part-2/

Used commands just for memory:
protoc -I. --go_out=plugins=micro:. proto/vessel/vessel.proto
protoc -I. --go_out=plugins=micro:. proto/consignment/consignment.proto
docker build -t shippy-service-vessel .
docker build -t shippy-service-consignment .
docker build -t shippy-cli-consignment .
docker container ls/stop/rm
docker rm $(docker ps -a -q) - remove all stopped containers
git push origin master

All golang code and the docker files work at 16.03.2020.

This example of work between the service shippy-service-consignment and shippy-cli-consignment.
[vvp@vvp-ThinkCentre-M93p] ~/workspace/golang_workspace/src/my_own_projects/microservice-in-golang/shippy-service-consignment> docker run -p 50051:50051 -e MICRO_SERVER_ADDRESS=:50051 shippy-service-consignment
2020-03-16 13:30:56.772105 I | Transport [http] Listening on [::]:50051
2020-03-16 13:30:56.772182 I | Broker [http] Connected to [::]:46025
2020-03-16 13:30:56.772371 I | Registry [mdns] Registering node: shippy.service.consignment-b813478e-b8d8-4465-ba3c-9436fa47c6

[vvp@vvp-ThinkCentre-M93p] ~/workspace/golang_workspace/src/my_own_projects/microservice-in-golang/shippy-cli-consignment> docker run shippy-cli-consignment
2020-03-16 13:38:45.582103 I | Created: true
2020-03-16 13:38:45.583163 I | description:"This is a test consignment" weight:55000 containers:<customer_id:"cust001" origin:"Manchester, United Kingdom" user_id:"user001" > containers:<customer_id:"cust002" origin:"Derby, United Kingdom" user_id:"user001" > containers:<customer_id:"cust005" origin:"Sheffield, United Kingdom" user_id:"user001" >

The output shows how the shippy-service-consignment and shippy-cli-consignment connect with each other. The sshippy-cli-consignment waits the request and reply.

This example of work between three services shippy-service-consignment, shippy-service-vessel and shippy-cli-consignment.
Firstly, run shippy-service-vessel, next shippy-service-consignment and shippy-cli-consignment.
[vvp@vvp-ThinkCentre-M93p] ~/workspace/golang_workspace/src/my_own_projects/microservice-in-golang/shippy-service-vessel> docker run -p 50052:50051 -e MICRO_SERVER_ADDRESS=:50051 shippy-service-vessel
2020-03-16 15:03:57.397172 I | Transport [http] Listening on [::]:50051
2020-03-16 15:03:57.397233 I | Broker [http] Connected to [::]:34333
2020-03-16 15:03:57.397401 I | Registry [mdns] Registering node: shippy.service.vessel-38100883-1bb6-4d3f-9896-dafb3e96a363

[vvp@vvp-ThinkCentre-M93p] ~/workspace/golang_workspace/src/my_own_projects/microservice-in-golang/shippy-service-consignment> docker run -p 50051:50051 -e MICRO_SERVER_ADDRESS=:50051 shippy-service-consignment
2020-03-16 15:02:48.910462 I | Transport [http] Listening on [::]:50051
2020-03-16 15:02:48.910521 I | Broker [http] Connected to [::]:45763
2020-03-16 15:02:48.910650 I | Registry [mdns] Registering node: shippy.service.consignment-1ba27d50-5aa7-4001-8995-aead18dc723f
2020-03-16 15:03:05.404856 I | Found vessel: Boaty McBoatface 

[vvp@vvp-ThinkCentre-M93p] ~/workspace/golang_workspace/src/my_own_projects/microservice-in-golang/shippy-cli-consignment> docker run shippy-cli-consignment
2020-03-16 15:04:07.809183 I | Created: true
2020-03-16 15:04:07.810198 I | description:"This is a test consignment" weight:55000 containers:<customer_id:"cust001" origin:"Manchester, United Kingdom" user_id:"user001" > containers:<customer_id:"cust002" origin:"Derby, United Kingdom" user_id:"user001" > containers:<customer_id:"cust005" origin:"Sheffield, United Kingdom" user_id:"user001" > vessel_id:"vessel001" 

Here is important to see the line "Found vessel: Boaty McBoatface" in the output of service shippy-service-consignment. It mean that shippy-service-vessel also works. Apart from that you should see the suffix "vessel_id:"vessel001" in the output of shippy-cli-consignment. Without the service shippy-service-vessel there is no such suffix in the output.

The output of "docker container ls"
[vvp@vvp-ThinkCentre-M93p] ~/workspace/golang_workspace/src/my_own_projects/microservice-in-golang/shippy-cli-consignment> docker container ls
CONTAINER ID        IMAGE                        COMMAND                  CREATED             STATUS              PORTS                      NAMES
70924fbf463d        shippy-service-consignment   "./shippy-service-co…"   3 hours ago         Up 3 hours          0.0.0.0:50051->50051/tcp   practical_sinoussi
fbe928556ee1        shippy-service-vessel        "./shippy-service-ve…"   3 hours ago         Up 3 hours          0.0.0.0:50052->50051/tcp   confident_goldwasser

