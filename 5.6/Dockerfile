FROM centos:centos7

# MySQL image for DeployDock.
#
# Volumes:
#  * /var/lib/mysql/data - Datastore for MySQL
# Environment:
#  * $MYSQL_USER - Database user name
#  * $MYSQL_PASSWORD - User's password
#  * $MYSQL_DATABASE - Name of the database to create
#  * $MYSQL_ROOT_PASSWORD (Optional) - Password for the 'root' MySQL account

MAINTAINER Eric Sites <eric@meros.io>

ENV MYSQL_VERSION=5.6 \
    HOME=/var/lib/mysql

LABEL io.k8s.description="MySQL is a multi-user, multi-threaded SQL database server" \
      io.k8s.display-name="MySQL 5.6" \
      io.deploydock.expose-services="3306:mysql" \
      io.deploydock.tags="database,mysql,mysql56,rh-mysql56"

EXPOSE 3306

# This image must forever use UID 27 for mysql user so our volumes are
# safe in the future. This should *never* change, the last test is there
# to make sure of that.
RUN rpmkeys --import file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7 && \
    yum -y --setopt=tsflags=nodocs install https://www.softwarecollections.org/en/scls/rhscl/rh-mysql56/epel-7-x86_64/download/rhscl-rh-mysql56-epel-7-x86_64.noarch.rpm && \
    yum -y --setopt=tsflags=nodocs install gettext hostname bind-utils rh-mysql56 && \
    yum clean all && \
    mkdir -p /var/lib/mysql/data && chown mysql.mysql /var/lib/mysql/data && \
    test "$(id mysql)" = "uid=27(mysql) gid=27(mysql) groups=27(mysql)"

COPY run-*.sh /usr/local/bin/
COPY contrib /var/lib/mysql/

# Loosen permission bits to avoid problems running container with arbitrary UID
RUN chmod -R a+rwx /var/lib/mysql

# When bash is started non-interactively, to run a shell script, for example it
# looks for this variable and source the content of this file. This will enable
# the SCL for all scripts without need to do 'scl enable'.
ENV BASH_ENV=/var/lib/mysql/scl_enable \
    ENV=/var/lib/mysql/scl_enable \
    PROMPT_COMMAND=". /var/lib/mysql/scl_enable"

VOLUME ["/var/lib/mysql/data"]

USER 27

ENTRYPOINT ["run-mysqld.sh"]
CMD ["mysqld"]
