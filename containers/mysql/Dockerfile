FROM mysql:8

LABEL maintainer="Hari Venu V <harivenu.v1992@gmail.com>"

# Ensure permissions on /etc/mysql/conf.d
# If the directory is empty, avoid errors with || true
RUN find /etc/mysql/conf.d -type f -exec chmod 775 {} \; || true

# You can add custom MySQL configuration files here (if needed)
# COPY ./my-custom.cnf /etc/mysql/conf.d/

# Make sure MySQL starts with default configuration
CMD ["mysqld"]

