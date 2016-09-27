# Openvpn docker-compose setup
[![Docker Stars](https://img.shields.io/docker/stars/enoniccloud/openvpn.svg)](https://hub.docker.com/r/enoniccloud/openvpn/)
[![Docker Pulls](https://img.shields.io/docker/pulls/enoniccloud/openvpn.svg)](https://hub.docker.com/r/enoniccloud/openvpn/)


This is a docker image forked out of the [kylemanna/docker-openvpn](https://github.com/kylemanna/docker-openvpn) repo to be used in Enonic Cloud. Feel free to use it on your own server too as this is made to be compatible with a generic docker-compose setup too.

## Installation
- Create a `docker-compose.yml` file in your repo and add the following content:
```
openvpn:
  image: enoniccloud/openvpn
  restart: always
  ports:
    - "1194:1194/tcp"
  volumes_from:
    - "storage"
  cap_add:
    - NET_ADMIN
  labels:
    io.enonic.backup.data: '/etc/openvpn'
storage:
  image: busybox
  volumes:
      - '/etc/openvpn'

```
- Commit it to your projects repo and build it.
- Verify that your docker installation is connected to the right host, and run the following commands to configure Openvpn:
  - `docker-compose run --rm openvpn ovpn_genconfig -u udp://your.servers.name.com`
  - `docker-compose run --rm openvpn ovpn_initpki`
- Now start up your Openvpn project: `docker-compose up -d openvpn`


## Adding client certificates
- Connect to your docker host.
- Create a passwordless certificate for the user: `docker-compose run --rm openvpn easyrsa build-client-full CLIENTNAME nopass`
- Download the client configuration with embedded certificates: `docker-compose run --rm openvpn ovpn_getclient CLIENTNAME > CLIENTNAME.ovpn`

## Removing client certificates
- Revoke the client certificate: `docker-compose run --rm openvpn easyrsa revoke CLIENTNAME`
- Generate crl: `docker-compose run --rm openvpn easyrsa gen-crl`


## List client certificates
- Run the following commands to list certificates: `docker-compose run --rm openvpn ovpn_listclients`
