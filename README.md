# Docker helpers

## Traefik

## MkCert

To use secured https:
https://github.com/FiloSottile/mkcert

## Instruction

### Configure DNSMasq
https://guewen.github.io/blog/2018/04/12/docker-traefik-and-dnsmasq/

### Create network
`docker network create traefik-public`

### Create certificates
- `cd docker/traefik/certs`
- `mkcert -install`
- `mkcert "*.docker.localhost"`

### Run env
```
docker-compose up -d
```

### Resources
- https://traefik.docker.localhost/dashboard/#/
- https://portainer.docker.localhost/#/auth
- https://whoami.docker.localhost/

## Example for Ubuntu
```bash
# docker
sudo apt-get remove docker docker-engine docker.io containerd runc
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
sudo apt install docker docker-compose
sudo docker network create traefik-public
sudo groupadd docker
sudo usermod -aG docker $USER

# dnsmasq to have own dns
sudo apt install dnsmasq
# change these files according to dnsmasq docs:
sudo vim /etc/dnsmasq.conf
sudo vim /etc/dnsmasq.d/localhost.conf
sudo vim /etc/dhcp/dhclient.conf
systemctl restart dnsmasq
systemctl enable dnsmasq
sudo dhclient

# mkcert
sudo apt install libnss3-tools
sudo apt install mkcert
wget https://github.com/FiloSottile/mkcert/releases/download/v1.4.3/mkcert-v1.4.3-linux-amd64
mv mkcert-v1.4.3-linux-amd64 mkcert
sudo chmod +x mkcert
sudo mv mkcert /usr/local/bin
mkcert -install
mkcert -CAROOT
openssl x509 -in rootCA.pem -inform PEM -out rootCA.crt
cd /usr/share/ca-certificates
sudo mkdir extra
sudo cp /home/{USER_CAROOT}/.local/share/mkcert/rootCA.crt ./extra
sudo dpkg-reconfigure ca-certificates

# install GitHub GH Cli
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-key C99B11DEB97541F0
sudo apt-add-repository https://cli.github.com/packages
sudo apt update
sudo apt install gh
gh auth login

# dockerizer with traefic
gh repo clone zagovorichev/dockerizer

# check that noone uses ports that dockerizer needs
netstat -pna | grep 80
# I found apache there, so destory it
sudo apt-get purge apache2 apache2-utils apache2.2-bin apache2-common
sudo apt-get purge apache2 apache2-utils apache2-bin apache2.2-common

# generate cerificates
cd docker/traefik/certs
mkcert "*.docker.localhost"
mkcert "*.docker"


# run container
docker-compose up -d
```
