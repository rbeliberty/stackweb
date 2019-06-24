FROM nginx:1.14

RUN apt-get update && apt-get upgrade -y

RUN apt-get install -y \
  build-essential \
  apt-utils \
  ca-certificates \
  apt-transport-https \
  gnupg \
  wget \
  curl \
  libcurl4-openssl-dev \
  nano \
  git \
  zip \
  iputils-ping \
  vim \
  dnsutils \
  net-tools \
  htop

COPY nginx.conf /etc/nginx/nginx.conf
ADD sites-enabled /etc/nginx/sites-enabled
RUN rm -rf /etc/nginx/conf.d/default.conf

WORKDIR /var/www/app/public