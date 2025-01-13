FROM mariadb:10.4

# Install wget and unzip
RUN apt-get update && apt-get upgrade -y 

# Set up working directory
WORKDIR /tmp


COPY sample_db.sql /docker-entrypoint-initdb.d/

# Ensure scripts in docker-entrypoint-initdb.d are executable
RUN chmod -R +x /docker-entrypoint-initdb.d

# Use the base entrypoint
CMD ["mysqld"]
