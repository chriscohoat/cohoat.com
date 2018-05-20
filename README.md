## Deploying this site

0) Create the Docker network

```
docker network create --driver bridge chriscohoat.com
```

1) Build the Docker images

```
docker build -t chriscohoat/chriscohoat.com-nginx -f dockerfiles/nginx/Dockerfile .
docker build -t chriscohoat/chriscohoat.com-orthanc -f dockerfiles/orthanc/Dockerfile .
```

2) Run the Docker images

Orthanc (medical imaging software):

```
docker run --network chriscohoat.com --name=orthanc --rm -d \
  -v ~/workspaces/chriscohoat/chriscohoat.com/config/orthanc.json:/etc/orthanc/orthanc.json:ro \
  -v ~/workspaces/chriscohoat/chriscohoat.com/data/orthanc-db/:/var/lib/orthanc/db/ \
  chriscohoat/chriscohoat.com-orthanc
```

Nginx server:

```
docker run --network chriscohoat.com --name=nginx --rm -d \
 -p 8000:80 \
 chriscohoat/chriscohoat.com-nginx
```

## Allowing upload / deletion

The default nginx configuration disables upload/deletion of patients and studies.
Comment out the `add_header` and `if` statements in `config/nginx/conf.d/chriscohoat.com.conf`
to allow editing. In the future this will be an authentication plugin of some sort.