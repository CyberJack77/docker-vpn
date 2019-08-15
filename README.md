# docker-vpn

A docker-compose example for using openvpn as network_mode.

This example creates 3 docker containers:

* A openvpn container.
* A jackett container. 
* A nginx container (optional).

The openvpn container is responsible for connecting to your vpn provider.

The jackett container is a example service that uses the openvpn container for network traffic. It can only access the internet through the vpn container, thus using the vpn connection.

The optional nginx container acts as a reverse proxy to access the jackett container over a domain name (or ip and port number). This container uses the openvpn container to access the jackett container.

## Requirements

- docker
- docker-compose

**Optional:**

- traefik (see docker-compose-traefik.yml)

## Setup

- Create a openvpn configruation file and place it in the `config/openvpn` directory.
- Edit the `docker-compose.yml` file and replace the openvpn container command line to match you openvpn configuration file. You can also provide other openvpn parameters here. The example has 3 extra parameters.

		command: --config /vpn/my-config.ovpn --auth-nocache --sndbuf 262144 --rcvbuf 262144

- Update the `port` configuration of your port 80 is not available.

### Traefik

When using traefik, create a `web` network (or use a already created docker network), follow the setup steps, but modify the `docker-compose-traefik.yml` file instead.

	docker network create web
		
Update the traefik configuration `docker-compose-traefik.yml` and replace the `.....` with a domain name on this line:

    - "traefik.frontend.rule=Host:....."
		
When running add the `-f docker-compose-traefik.yml` parameter to the `docker-compose` commands.

## Running

Build the nginx image

	docker-compose build nginx_jackett
	
	// or when using traefik
	docker-compose -f docker-compose-traefik.yml build nginx_jackett
		 
Start the docker containers and watch the logging for errors.

	docker-compose up
	
	// or when using traefik
	docker-compose -f docker-compose-traefik.yml up

Test the nginx containers by opening a browser and point to `http://[domain/ip]`. When using a differt port, add `:[port number` to the url. When using traefik, just open the domain name specified.

If all works fine, stop the containers (`CRTL+c`) and start them again in detached mode.

	docker-compose up -d
	
	// or when using traefik
	docker-compose -f docker-compose-traefik.yml up -d

## Modifing the nginx configuration

If you use a other service than jackett (or multiple services), you may need to change the nginx configuration. The files `config/nginx/jackett/Dockerfile` and `config/nginx/jackett/nginx.conf` both contain service names and port numbers. Make sure they match the correct service name and port otherwise the reverse proxy won't work.

