# docker-rds-haproxy
a docker build of a customized haproxy for mysql connections that uses AWS APIs to discover RDS nodes
### Getting started
Install the docker toolset if you don't have it yet, or update it if it's not the latest and greatest with homebrew: 
```bash
brew update && brew install docker docker-machine docker-compose
```
(or download the docker toolbox package at https://www.docker.com/products/docker-toolbox)

Then, install the [awscli](http://docs.aws.amazon.com/cli/latest/userguide/installing.html) and the [ecscli](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_CLI_installation.html) if you intend to run this on [ECS](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html). 
### Test with docker-machine
```bash
docker-machine create local --driver virtualbox 
```
Once completed, just run 
```bash
eval $(docker-machine env local)
``` 
to be able to access the docker daemon running on your docker machine and then
```bash
ENABLE_STATS=true DB_NODES="db1.example.com db2.example.com" docker-compose up -d && docker-compose logs
``` 
where `DB_NODES` is a space separate list of MySQL servers listening on port 3306
You can check the status of haproxy if you run `open http://$(docker-machine ip local):8080/stats` and connect to your mysql databases with `mysql -h $(docker-machine ip local)`

### Test with docker-machine on AWS
To test the environment on AWS, the simplest thing is to create an instance on your VPC still using docker machine:
```bash
docker-machine create --driver amazonec2 --amazonec2-iam-instance-profile <role-with-RDS-read-access> --amazonec2-vpc-id <vpc-where-your-RDS-instance-resides> --amazonec2-subnet-id <subnet-in-your-vpc> --amazonec2-region <region-of-your-vpc> --amazonec2-zone <the-zone-for-your-subnets> haproxy-vm
```
```
