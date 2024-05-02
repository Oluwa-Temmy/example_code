**_Summary: How to use 
your env files during build time w/o having the env files on docker. Using volumes and docker-compose.yml files_**

When building your application container DockerFile should not have commands such as

```collectstatic```

```runserver```

```makemigrations```</br>

Instead we would have these in our docker-compose.example.yml under our ```command:``` in our build_app service. 


### Volumes 
```./host/machine/path/.env:./container/path/.env```

To my understanding volumes in this context 
acts more like a pointer. You input the file that
is on your host machine that you want your container
to look at for data. But it's only 'mounted' onto
the container path. It is not public or on your
docker container.

Docker Volumes: https://docs.docker.com/storage/volumes/
Docker Bind Mounts: https://docs.docker.com/storage/bind-mounts/

### Reading and configuring the .env file

In return you do have to configure your settings.py
to read from the container path and not the path on 
your machine when running your application in the conatiner.



When running the server on your machine, you read the .env file path from your machine

_Note: using os to append path get env variables_

```
import os
host_path = '/host/machine/path/.env'

os.environ['PATH'] += os.pathsep + host_path
```

_Note: using python decouple to set path to read from_

```
from decouple import Config, RepositoryEnv

host_path = Config(RepositoryEnv('/host/machine/path/.env'))

SECRET_KEY = host_path('SECRET_KEY')
```


But when running your application in the container you may need to configure your settings.py or other files to read from the containers path to the .env

_Note: using os to append path get env variables_

```
import os
container_path = 'container/path/.env'

os.environ['PATH'] += os.pathsep + container_path
```

_Note: using python decouple to set path to read from_

```
from decouple import Config, RepositoryEnv

container_path = Config(RepositoryEnv('container/path/.env'))

SECRET_KEY = container_path('SECRET_KEY')
```

Experiment with it to understand the concept better