FROM debian:stretch-slim

MAINTAINER Chris Cohoat <chris@cohoat.com>

WORKDIR /root/

RUN apt-get update && apt-get install -y python python-dev python-pip vim htop telnet

RUN pip install httplib2

ADD ./orthanc/bin/setup-locales.sh ./setup-locales.sh
RUN bash ./setup-locales.sh

ADD ./orthanc/bin/download-orthanc.sh ./download-orthanc.sh
RUN bash ./download-orthanc.sh

ADD ./orthanc/bin/create-config.sh ./create-config.sh
RUN bash ./create-config.sh

ADD ./orthanc/bin/download-plugins.sh ./download-plugins.sh
RUN bash ./download-plugins.sh

RUN rm ./setup-locales.sh ./download-orthanc.sh ./create-config.sh ./download-plugins.sh

COPY ./config/orthanc.json /etc/orthanc/orthanc.json

COPY ./orthanc/bin/ImportDicomFiles.py ./ImportDicomFiles.py

VOLUME [ "/var/lib/orthanc/db" ]
EXPOSE 4242
EXPOSE 8042

ENTRYPOINT [ "Orthanc" ]
CMD [ "/etc/orthanc/" ]