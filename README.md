## Running this site locally

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

## Deploying

1) Clone repo:

```
git clone https://github.com/chriscohoat/cohoat.com.git
```

2) Install Docker:

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
apt-cache policy docker-ce
sudo apt-get install -y docker-ce
sudo usermod -aG docker ${USER}
exit
```

3) Build Docker images:

```
cd cohoat.com
docker build -t chriscohoat/cohoat.com-nginx -f dockerfiles/nginx/Dockerfile .
docker build -t chriscohoat/cohoat.com-orthanc -f dockerfiles/orthanc/Dockerfile .
```

4) Transfer the DICOM seed files to the server and into `orthanc/seed`

5) Run Orthanc with the seed directory linked:

```
docker run --network cohoat.com --name=orthanc --rm -d \
  -v ~/cohoat.com/data/orthanc-db/:/var/lib/orthanc/db/ \
  -v ~/cohoat.com/orthanc/seed/:/root/seed/ \
  chriscohoat/cohoat.com-orthanc
```

and then run:

```
docker exec -it orthanc python ImportDicomFiles.py 127.0.0.1 8042 ./seed
```

6) Run the Docker images

Orthanc (medical imaging software):

```
docker run --network cohoat.com --name=orthanc --rm -d \
  -v ~/cohoat.com/data/orthanc-db/:/var/lib/orthanc/db/ \
  chriscohoat/cohoat.com-orthanc
```

Nginx server:

```
docker run --network cohoat.com --name=nginx --rm -d \
 -p 8000:80 \
 chriscohoat/cohoat.com-nginx
```