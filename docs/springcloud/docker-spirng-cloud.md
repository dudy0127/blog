https://programmaticponderings.com/2017/02/28/distributed-service-configuration-with-consul-spring-cloud-and-docker-2/



visualizer 可视化

docker service create \
  --name=viz \
  --publish=5001:8080/tcp \
  --constraint=node.role==manager \
  --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
  dockersamples/visualizer



