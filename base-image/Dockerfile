FROM ubuntu:latest

RUN echo "==> Installing Packages..."

RUN apt update \
 && apt install -y git

COPY ./softwares/* /opt/

RUN cd /opt/ \
 && for file in *.tar.gz; do tar xzvf "${file}" \
 && chown -R root:root . \
 && rm "${file}"; done

ENV JAVA_HOME=/opt/jdk1.8.0_261
ENV MAVEN_HOME=/opt/apache-maven-3.2.5
ENV NODE_HOME=/opt/node-v8.16.2-linux-x64
ENV PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin:$NODE_HOME/bin:.
