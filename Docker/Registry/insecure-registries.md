# Error While Accessing Local Registries
Example :
```bash
root@srv1:/home/competitor/httpd# docker build . -t web-httpd:latest
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  3.072kB
Step 1/2 : FROM srv1.artemis.local:1234/httpd:base
Get "https://srv1.artemis.local:1234/v2/": tls: failed to verify certificate: x509: certificate relies on legacy Common Name field, use SANs instead
```
## Fix
Create file daemon.json in /etc/docker
```bash
root@srv1:/home/competitor/httpd# nano /etc/docker/daemon.json
```
```json
{
        "insecure-registries" : [https://srv1.artemis.local:1234]
}
```
then restart Docker
> - https://docs.docker.com/registry/insecure/
