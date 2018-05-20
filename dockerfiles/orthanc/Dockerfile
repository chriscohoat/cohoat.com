FROM debian:stretch-slim

MAINTAINER Chris Cohoat <chris@cohoat.com>

WORKDIR /root/

ADD ./orthanc/setup-locales.sh ./setup-locales.sh
RUN bash ./setup-locales.sh

ADD ./orthanc/download-orthanc.sh ./download-orthanc.sh
RUN bash ./download-orthanc.sh

ADD ./orthanc/create-config.sh ./create-config.sh
RUN bash ./create-config.sh

ADD ./orthanc/download-plugins.sh ./download-plugins.sh
RUN bash ./download-plugins.sh

RUN rm ./setup-locales.sh ./download-orthanc.sh ./create-config.sh ./download-plugins.sh

VOLUME [ "/var/lib/orthanc/db" ]
EXPOSE 4242
EXPOSE 8042

ENTRYPOINT [ "Orthanc" ]
CMD [ "/etc/orthanc/" ]