# Set php version arg
ARG VERSION

FROM nginx:$VERSION-alpine

ENV HOME /root

RUN echo "NGINX VERSION $VERSION"

RUN apk update && apk upgrade

COPY default.conf /etc/nginx/conf.d/

# Install essential tools
RUN apk add --no-cache --virtual .build-deps \
    zsh \
    git \
    nmap \
    nss \
    nano 

# Download oh-my-zsh
RUN sh -c "$(wget  https://github.com/FiloSottile/mkcert/releases/download/v1.4.1/mkcert-v1.4.1-linux-amd64 -O mkcert)"
RUN chmod +x mkcert
RUN mv mkcert /usr/local/bin

WORKDIR /ca
ENV CAROOT /ca
RUN sh -c "mkcert -install -client"

WORKDIR /certs
RUN sh -c "mkcert -key-file local-key.pem -cert-file local-cert.pem *.muylocal.com"

WORKDIR /var/www

# Download oh-my-zsh
RUN sh -c "$(wget -O- https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
# Set "gnzh" as theme fot zsh terminal
RUN sed -i 's/ZSH_THEME="robbyrussell"/ZSH_THEME="gnzh"/g' ~/.zshrc
#Set zsh has default shell executable

RUN sed -i -e "s/bin\/bash/bin\/zsh/" /etc/passwd

EXPOSE 80
EXPOSE 443

CMD ["nginx", "-g", "daemon off;"]