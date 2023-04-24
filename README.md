# ELK download on ubuntu 22.04 using Docker and Docker Compose
Using Docker and Docker Compose to download Elasticsearch and Kibana has several benefits:

- Easy installation: Docker makes it easy to install and run applications, including Elasticsearch and Kibana. With Docker, you don't need to worry about installing the correct version of Java or setting up other dependencies.

- Consistent environment: Docker ensures that the environment in which Elasticsearch and Kibana run is consistent, regardless of the host operating system. This means that you can run Elasticsearch and Kibana on any machine that has Docker installed, without worrying about compatibility issues.

- Scalability: Docker and Docker Compose make it easy to scale Elasticsearch and Kibana horizontally by adding or removing containers. This allows you to handle increasing amounts of data or traffic without having to manually configure new instances.

- Portability: With Docker, you can easily move your Elasticsearch and Kibana instances between environments, such as development, testing, and production. This makes it easier to maintain consistency across different environments.

Overall, using Docker and Docker Compose to download Elasticsearch and Kibana is a convenient and efficient way to get up and running with these tools.

## Downloading Docker 
The first step is to refresh the repositories. To do so, run the command:

```sudo apt update```

Install the necessary packages to allow apt to use a repository over HTTPS:

```sudo apt install apt-transport-https ca-certificates curl software-properties-common```

Add the Docker GPG key:

```curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg```

Add the Docker repository to your system:

```echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null```

Update the package index again:

```sudo apt update```

Install Docker:

```sudo apt install docker-ce```

The daemon started, and the process enabled to start on boot. Check that it’s running:

`sudo systemctl status docker`
The output should show that the service is active and running.

## Downloading Docker Compose
Install curl if it is not already installed:

`sudo apt install curl`

Download the latest version of Docker Compose by running the following command:

`mkdir -p ~/.docker/cli-plugins/`

```curl -SL https://github.com/docker/compose/releases/download/v2.3.3/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose```

Apply executable permissions to the Docker Compose binary:

`chmod +x ~/.docker/cli-plugins/docker-compose`

Verify that Docker Compose is installed correctly

`docker compose version`

## Downloading Elasticsearch and Kibana
Create the docker-compose file :

`nano elastic.yaml`

Put below contents in the file:

```version: '3.7'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.4.0
    container_name: elasticsearch
    restart: always
    environment:
      - xpack.security.enabled=false
      - discovery.type=single-node
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    cap_add:
      - IPC_LOCK
    volumes:
      - elasticsearch-data-volume:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
      - 9300:9300 

  kibana:
    container_name: kibana
    image: docker.elastic.co/kibana/kibana:7.4.0
    restart: always
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    ports:
      - 5601:5601
    depends_on:
      - elasticsearch

volumes:
  elasticsearch-data-volume:
    driver: local
  ``` 
    
 press Ctrl+X and click on Y to save the file. Then, run the yaml file using the command: `sudo docker compose -f elastic.yaml up`. The initial execution of this command may take a few minutes to complete.
 
 Note that Elasticsearch will be running on port 9200 and kibana will be running on port 5601.

## Downloading Logstash
