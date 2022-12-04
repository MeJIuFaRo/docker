## Настройка сетевого реестра c регистрацией.

## A. Узел dock01.

``` bash

0. mkdir -p ~/docker/certs && cd ~/docker

1. openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/my.key -x509 -days 3650 -out certs/my.crt -addext 'subjectAltName=DNS:dock01'

2. sudo mkdir -p /etc/docker/certs.d/dock01:5002

3. sudo cp certs/my.crt /etc/docker/certs.d/dock01\:5002/ca.crt

4. sudo service docker restart

5. sudo apt install apache2-utils

6. mkdir ~/docker/auth

7. htpasswd -Bbn sand pass >> ~/docker/auth/htpasswd  # user: sand, password: pass

8. docker run --name registry2 -d -p 5002:5000 
-v /home/student/docker/certs:/certs \
-v /home/student/docker/auth:/auth \ 
-e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/my.crt \
-e REGISTRY_HTTP_TLS_KEY=/certs/my.key \
-e REGISTRY_AUTH=htpasswd \
-e REGISTRY_AUTH_HTPASSWD_REALM="Registry Realm" \
-e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
--restart=always registry:2

9. scp certs/my.crt dock02:/home/student/ca.crt

10. docker tag local_image dock01:5002/image:tag

11. docker push dock01:5002/image:tag
```

## B. Узел dock02.

``` shell
1. sudo apt install ssh -y

2. sudo mkdir -p /etc/docker/certs.d/dock01:5002

3. sudo cp ca.crt /etc/docker/certs.d/dock01\:5002/ca.crt

4. sudo service docker restart

5. docker login dock01:5002

6. docker pull dock01:5002/image:tag

7. docker logout dock01:5002

```
