FROM alpine:3.18.4
LABEL maintainer="Guilherme Bigois"

#Download the desired package(s)
RUN curl -O https://download.microsoft.com/download/3/5/5/355d7943-a338-41a7-858d-53b259ea33f5/msodbcsql18_18.3.2.1-1_$architecture.apk
RUN curl -O https://download.microsoft.com/download/3/5/5/355d7943-a338-41a7-858d-53b259ea33f5/mssql-tools18_18.3.1.1-1_$architecture.apk

#(Optional) Verify signature, if 'gpg' is missing install it using 'apk add gnupg':
curl -O https://download.microsoft.com/download/3/5/5/355d7943-a338-41a7-858d-53b259ea33f5/msodbcsql18_18.3.2.1-1_$architecture.sig
curl -O https://download.microsoft.com/download/3/5/5/355d7943-a338-41a7-858d-53b259ea33f5/mssql-tools18_18.3.1.1-1_$architecture.sig

curl https://packages.microsoft.com/keys/microsoft.asc  | gpg --import -
gpg --verify msodbcsql18_18.3.2.1-1_$architecture.sig msodbcsql18_18.3.2.1-1_$architecture.apk
gpg --verify mssql-tools18_18.3.1.1-1_$architecture.sig mssql-tools18_18.3.1.1-1_$architecture.apk

#Install the package(s)
sudo apk add --allow-untrusted msodbcsql18_18.3.2.1-1_$architecture.apk
sudo apk add --allow-untrusted mssql-tools18_18.3.1.1-1_$architecture.apk

# Liberação da porta de comunicação
EXPOSE 7890

# Instalação da ODBC PostgreSQL
RUN apt-get update -y
RUN apt install odbc-postgresql unixodbc -y

# Descompactação do binário
COPY ./data/dbaccess.tar.gz /tmp
# RUN mv /tmp/dbaccess.tar.gz /tmp/dbaccess.tar.gz
RUN mkdir /usr/lib/dbaccess
RUN tar -xvzf /tmp/dbaccess.tar.gz -C /usr/lib/dbaccess
RUN rm -rf /tmp/dbaccess.tar.gz

# Exclusão de arquivos desnecessários
RUN rm -rf /usr/lib/dbaccess/dbmonitor

# Criação do arquivo .INI
ARG APP_SECRET
RUN /usr/lib/dbaccess/tools/dbaccesscfg -u sa -p "$APP_SECRET" -d P12R2210 -a PROTHEUS -o "clientlibrary=/usr/lib/x86_64-linux-gnu/libodbc.so.1" -g "licenseserver=localhost;licenseport=5555"
RUN mv /dbaccess.ini /usr/lib/dbaccess/dbaccess.ini

# Adição do driver PostgreSQL no UnixODBC
COPY ./data/odbcinst.ini /tmp
RUN odbcinst -i -d -f /tmp/odbcinst.ini
RUN rm -rf /tmp/odbcinst.ini

# Adição da conexão ODBC
COPY ./data/odbc.ini /tmp
RUN odbcinst -i -s -f /tmp/odbc.ini
RUN cp /root/.odbc.ini /etc/odbc.ini
RUN rm -rf /tmp/odbc.ini

# Definição do diretório inicial
WORKDIR /usr/lib/dbaccess

# Inicialização do serviço
CMD ["./multi/dbaccess64"]
