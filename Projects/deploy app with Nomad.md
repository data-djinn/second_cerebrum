[[Nomad]] [[Terraform]] [[Packer]] [[Docker]]

[schmichael/django-nomadrepo: Demo app (github.com)](https://github.com/schmichael/django-nomadrepo)
[Deploy Your First App with HashiCorp Nomad in 20 mins - YouTube](https://www.youtube.com/watch?v=SSfuhOLfJUg)
#### Docker Postgres container 
```shell
docker run \
  --name example \
  -e POSTGRESS_PASSWORD=...\
  -p 5432:5432 \
  -d postgres:13
```

#### Django dev server
`python manage.py runserver`

#### `Dockerfile`
```Dockerfile
FROM python:3.9-slim-buster
MAINTAINER sean "m.sean.lawrence@pm.me"

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && \
    apt-get install -y build-essential libpq-dev \
    pyhon-dev
    
RUN python3 -m venv /opt/venv

COPY requirements.txt .
RUN . /opt/venv/bin/activate && \
  pip install -r requirements.txt
  
COPY nomadrepo nomadrepo
CMD . /opt/venv/bin/activate && \
  exec python nomadrep/manage.py runserver --insecure 0.0.0.0:8000
```

#### publish to dockerhub
`docker push`

#### terraform
- create a cluster with:
  - nomad
  - consul

`terraform apply` to set up infra with load balancer -> django server -> consul service mesh -> postgres server

#### deploy postgres with nomad job spec:
```
group "db" {
  
    task "postgres" {
      driver = 'docker'
      config {
        image = 'postgres:13'
      }
      
      env {
        POSTGRES_PASSWORD="<TODO: Use vault to integrate secrets!>"
      }
      
      resources {
        cpu = 2000
        memory = 2000
      }
    }
}
```

#### Consul connect
```
group 'db' {
  network {
    mode = 'bridge'
  }
  
  service {
    name = 'nomadrepodb'
    port = '5432'
  
  connect {
    sidecar_service {}
  }
  }

tas
  