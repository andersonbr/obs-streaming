FROM tiangolo/nginx-rtmp

LABEL maintainer="Anderson Calixto <andersonbr@gmail.com>"

ENV NGINX_VERSION nginx-1.18.0
ENV NGINX_RTMP_MODULE_VERSION 1.2.1

# Install dependencies
RUN apt-get update && \
    apt-get install ffmpeg -y && \
    rm -rf /var/lib/apt/lists/*

EXPOSE 1935
EXPOSE 8888

CMD ["nginx", "-g", "daemon off;"]
