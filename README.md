# Eclipse Zenoh Router deployment using Docker tool and Docker Compose file
For quick test with default configuration go to [Docker Image Build Up](#docker-image-build-up).

- Zenoh REST port - default is **8000** 
- Zenoh TCP port - default is **7447**
- Docker container name - **zenoh_router**

Ports can be easly re-mapped by editing `compose.yaml` file.

***
## Zenoh Router configuration 
To change the configuration of the Zenohd router, you can specify own parameters in the `router.json5` configuration file. Default configuration include:
- rest plugin on 8000 HTTP port:
    ```json
    ...
    "plugins_search_dirs": [],                 
    "plugins": {                               
        "rest": {                                
        "__required__": true,                  
        "http_port": "8000"                    
    },
    ...
    ```
- storage manager with *demo* storage:
    ```json
    ...
    "storage_manager": {                       
        "storages": {                           
            "demo": {                            
            "key_expr": "demo/example/**",     
            "volume": "memory"                 
            }                                    
        }                                      
    }
    ...
    ```

More information about ZenohD can be found at https://github.com/eclipse-zenoh/zenoh.

***
## Docker Image Build Up
To deploy a working Zenoh router with your own configuration follow steps below:
1. Clone this repository, and move to `zenoh-router/` directory.

2. You may need to change permissions for the `entrypoint.sh` file on the host machine. Simply run:
    ```bash
    sudo chmod +x entrypoint.sh
    ```
3. Router works by default, but you can change parameters inside `router.json5` for your own needs.
4. Build the image:
    ```bash
    docker-compose -f compose.yaml up --build
    ```

***
## Interaction with working Zenoh Router using HTTP Requests

- hostIP
- zenohRESTport (**8000** by default)

### Zenoh router information:
```bash
curl http://<hostIP>:<zenohRESTport>/@/router/local
```

### Storage information:
```bash
curl "http://<hostIP>:<zenohRESTport>/@/router/local/status/plugins/storage_manager/storages/*"
```

### PUT key/value example:
```bash
curl -X PUT -H 'content-type:text/plain' -d 'Hello World!' http://<hostIP>:<zenohRESTport>/demo/example/test-message
```

### GET key/value example:
```bash
curl http://<hostIP>:<zenohRESTport>/demo/example/test-message
```
Example output:
```bash
[
{ "key": "demo/example/test-message", "value": "Hello World!", "encoding": "text/plain", "time": "2024-01-18T12:35:37.781402476Z/678ef664139c1214c3ba3844b5542b08" }
]
```
For more informations about key expressions in Zenoh refer to: 
https://zenoh.io/docs/manual/abstractions/
