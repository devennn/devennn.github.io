---
title: "Docker Container Network Communication"
date: "February 6, 2023"
excerpt: "Docker Network communication. Internal to Internal and Internal to External"
cover_image: "https://images.pexels.com/photos/821754/pexels-photo-821754.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1"
project: ""
---

In this post, I will write about how to communicate between microservices, both internally and externally.

The use cases I want to share today are:

  1) Both app containers are deployed using Docker.
  2) One app container communicates with a non-Docker app.

![Docker network tutorial](https://user-images.githubusercontent.com/49480914/216913799-79cdee95-49f3-4e77-af2c-efee082eaccb.jpg)

### Follow this step for case 1

1) Define a shared network

```
networks:
    my_shared_network:
      driver: bridge
```

3) Make sure every service recognies the network.

```
services:
  app1:
    build: ./app1
    container_name: app1
    command: uvicorn server:app --reload --host=0.0.0.0 --port=8001
    ports:
      - 8001:8001
    volumes:
      - ./app1:/app
    networks:
      my_shared_network:
        aliases:
          - app1
```

5) Define aliases for every service. In the case mentioned above, the alias is "app1."
6) Inside the app, make requests to the alias instead of "localhost."

```python
APP_2_ALIASES = "app2"

# Perform request to this url
url = "http://{}:8002/app2".format(APP_2_ALIASES)
res = requests.post(url, json={
    "text": text
})
```

### For case 2

1) Make request to "host.docker.internal"

```python
APP_3_ALIASES = "host.docker.internal"

# Perform request to the url using host.docker.internal
url = "http://{}:8003/app3".format(APP_3_ALIASES)
res = requests.post(url, json={
    "text": text
})
```

That's it. Refer to the [code for full example](https://github.com/devennn/blog-code/tree/master/networkplayground).

Result..

```
{
    "message": "Success",
    "reply_app_1": "Received in app1 => Request start",
    "call_result": "{\"message\":\"Success\",\"reply_app_2\":\"Received in app2 => {\\\"message\\\":\\\"Success\\\",\\\"reply_app_3\\\":\\\"Behold app3 => Request start\\\"}\"}"
}
```
