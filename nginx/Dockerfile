FROM ubuntu:14.04

MAINTAINER <Daniel Peter daniel@projekt-denkfabrik.de>

# ENV vars httpuser and httppass
ENV httpuser=testuser	\
    httppass=testpassword

# ENV vars ssl
ENV country="/C=DE" region="/ST=Hessen" organization="/O=projekt-denkfabrik" email="/emailAddress=develop@projekt-denkfabrik.de" 	

# Install nginx and apache2-utils
RUN apt-get update && apt-get install -y nginx && apt-get install -y apache2-utils && apt-get install -y openssl && apt-get install -y wget

# Install salt minion
#RUN wget -O - https://repo.saltstack.com/apt/ubuntu/ubuntu14/latest/SALTSTACK-GPG-KEY.pub | sudo apt-key add - && \
#    apt-get install -y software-properties-common && \
#    add-apt-repository ppa:saltstack/salt && \
#		apt-get update && apt-get install -y salt-minion

# Install ansible
RUN apt-get install -y software-properties-common && \
    apt-add-repository ppa:ansible/ansible && \
		apt-get update && \
		apt-get install -y ansible

# Create httppasswd and X509 cert
RUN htpasswd -bn ${httpuser} ${httppass} > /etc/nginx/conf.d/htpasswd && \
    mkdir auth && \
    openssl req -newkey rsa:4096 -nodes -sha256 -keyout auth/domain.key -x509 -days 365 -out auth/domain.crt -subj "${country}${region}${organization}${email}" && \
		cp auth/* /etc/nginx/conf.d/

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log
RUN ln -sf /dev/stderr /var/log/nginx/error.log


VOLUME ["/var/cache/nginx"]

EXPOSE 80 443

CMD ["nginx", "-g", "daemon off;"]
