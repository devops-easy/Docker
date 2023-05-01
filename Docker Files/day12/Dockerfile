## docker network create -d bridge --subnet "10.10.10.0/24" my_bridge

## docker container run -d --name m1 --network my_bridge alpine sleep 1d

## docker container run -d --name m2 --network my_bridge alpine sleep 1d

## docker network inspect my_bridge

## docker exec m1 ping -c 4 m2

## docker network connect my_bridge d1
##  docker exec m1 ping -c 4 d1