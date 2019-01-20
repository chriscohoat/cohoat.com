FROM nginx:1.13

MAINTAINER Chris Cohoat <chris@cohoat.com>

RUN apt-get update && apt-get install -y htop vim telnet

RUN rm /etc/nginx/conf.d/default.conf
ADD ./config/nginx/conf.d/chriscohoat.com.conf /etc/nginx/conf.d/

COPY src /usr/share/nginx/html

WORKDIR /usr/share/nginx/html
