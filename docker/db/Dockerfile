FROM mysql:5.7

RUN apt-get update && apt-get install -y locales
RUN sed -i -E 's/# (ja_JP.UTF-8)/\1/' /etc/locale.gen && locale-gen

COPY my.cnf /etc/mysql/conf.d/my.cnf