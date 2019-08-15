FROM nginx:alpine

ADD nginx.conf /etc/nginx/nginx.conf
ADD wait-for /usr/bin

CMD ["/usr/bin/wait-for", "jackett:9117", "--","nginx", "-g", "daemon off;"]
