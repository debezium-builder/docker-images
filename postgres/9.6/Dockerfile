FROM postgres:9.6

MAINTAINER Debezium Community
ENV PLUGIN_VERSION=v0.6.0

# Install the packages which will be required to get everything to compile
RUN apt-get update \ 
    && apt-get install -f -y --no-install-recommends \
        software-properties-common \
        build-essential \
        pkg-config \ 
        git \
        postgresql-server-dev-9.6 \
        libproj-dev \
    && apt-get clean && apt-get update && apt-get install -f -y --no-install-recommends \            
        liblwgeom-dev \              
    && add-apt-repository "deb http://ftp.debian.org/debian testing main contrib" \ 
    && apt-get update && apt-get install -f -y --no-install-recommends \
        libprotobuf-c-dev=1.2.* \
    && rm -rf /var/lib/apt/lists/*             
 
# Compile the plugin from sources and install it
RUN git clone https://github.com/debezium/postgres-decoderbufs -b $PLUGIN_VERSION --single-branch \
    && cd /postgres-decoderbufs \ 
    && make && make install \
    && cd / \ 
    && rm -rf postgres-decoderbufs            

# Copy the custom configuration which will be passed down to the server (using a .sample file is the preferred way of doing it by 
# the base Docker image)
COPY postgresql.conf.sample /usr/share/postgresql/postgresql.conf.sample

# Copy the script which will initialize the replication permissions
COPY /docker-entrypoint-initdb.d /docker-entrypoint-initdb.d
