FROM centos:centos7.5.1804

RUN yum -y update

RUN yum -y remove httpd httpd.x86_64 httpd-tools.x86_64
RUN yum -y remove epel-release

RUN cp -p -f /usr/share/zoneinfo/Japan /etc/localtime

RUN localedef -f UTF-8 -i ja_JP ja_JP.UTF-8

RUN yum install -y epel-release && \
    yum -y install https://rpms.remirepo.net/enterprise/remi-release-7.rpm
#    sed -i -e "s/enabled *= *1/enabled=0/g" /etc/yum.repos.d/ius.repo && \
#    sed -i -e "s/enabled *= *1/enabled=0/g" /etc/yum.repos.d/epel.repo

RUN yum --enablerepo=epel -y install nghttp2 && \
    yum -y install mailcap postfix system-logos telnet && \
    yum -y install openssl wget httpd httpd-devel mod_ssl nano && \
    wget http://download-ib01.fedoraproject.org/pub/epel/7/x86_64/Packages/b/brotli-1.0.7-5.el7.x86_64.rpm && \
    rpm -Uvh brotli-1.0.7-5.el7.x86_64.rpm && \
    yum -y install brotli && \
    yum -y install sqlite sqlite-devel && \
    yum -y install gcc gcc-c++ ruby-devel && \
    yum clean all

RUN gem install mailcatcher

COPY ssl/server.crt /etc/pki/tls/certs/
COPY ssl/server.key /etc/pki/tls/private/server.key
COPY ssl/server.csr /etc/pki/tls/private/server.csr

RUN sed -E -i -e 's/\/etc\/pki\/tls\/certs\/localhost\.crt/\/etc\/pki\/tls\/certs\/server.crt/g' /etc/httpd/conf.d/ssl.conf
RUN sed -E -i -e 's/\/etc\/pki\/tls\/private\/localhost\.key/\/etc\/pki\/tls\/private\/server.key/g' /etc/httpd/conf.d/ssl.conf

RUN sed -i -e "s|LoadModule mpm_prefork_module modules/mod_mpm_prefork.so|#LoadModule mpm_prefork_module modules/mod_mpm_prefork.so|" /etc/httpd/conf.modules.d/00-mpm.conf && \
    sed -i -e "s|#LoadModule mpm_event_module modules/mod_mpm_event.so|LoadModule mpm_event_module modules/mod_mpm_event.so|" /etc/httpd/conf.modules.d/00-mpm.conf

ARG DOCKER_UID=1000

RUN usermod -o -u ${DOCKER_UID} apache

COPY httpd-original.conf /etc/httpd/conf/httpd.conf

# Node.js
ARG node_ver=16.13.0
ARG node_path=/usr/local/lib/nodejs
ARG node_file_name=node-v${node_ver}-linux-x64
ENV PATH ${node_path}/${node_file_name}/bin:$PATH
RUN curl https://nodejs.org/dist/v${node_ver}/${node_file_name}.tar.xz > ${node_file_name}.tar.xz \
    && mkdir -p $node_path \
    && tar xJvf ${node_file_name}.tar.xz -C $node_path

EXPOSE 80 443

CMD ["/usr/sbin/httpd", "-DFOREGROUND"]