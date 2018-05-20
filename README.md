## Deploying this site

0) Create the Docker network

```
docker network create --driver bridge cohoat.com
```

1) Build the Docker images

```
docker build -t chriscohoat/cohoat.com-nginx -f dockerfiles/nginx/Dockerfile .
docker build -t chriscohoat/cohoat.com-orthanc -f dockerfiles/orthanc/Dockerfile .
```

2) Run the Docker images

Orthanc (medical imaging software):

```
docker run --network cohoat.com --name=orthanc --rm -d \
  -v ~/workspaces/chriscohoat/cohoat.com/data/orthanc-db/:/var/lib/orthanc/db/ \
  chriscohoat/cohoat.com-orthanc
```

Nginx server:

```
docker run --network cohoat.com --name=nginx --rm -d \
 -p 8000:80 \
 chriscohoat/cohoat.com-nginx
```

## Allowing upload / deletion

The default nginx configuration disables upload/deletion of patients and studies.
Comment out the `add_header` and `if` statements in `config/nginx/conf.d/cohoat.com.conf`
to allow editing. In the future this will be an authentication plugin of some sort.

## Seeding the initial DICOM data

Run Orthanc with the seed directory linked:

```
docker run --network cohoat.com --name=orthanc --rm -it \
  -v ~/workspaces/chriscohoat/cohoat.com/data/orthanc-db/:/var/lib/orthanc/db/ \
  -v ~/workspaces/chriscohoat/cohoat.com/orthanc/seed/:/root/seed/ \
  chriscohoat/cohoat.com-orthanc
```

and then run:

```
docker exec -it orthanc python ImportDicomFiles.py 127.0.0.1 8042 ./seed
```