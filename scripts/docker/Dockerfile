#  Licensed to the Apache Software Foundation (ASF) under one   *
#  or more contributor license agreements.  See the NOTICE file *
#  distributed with this work for additional information        *
#  regarding copyright ownership.  The ASF licenses this file   *
#  to you under the Apache License, Version 2.0 (the            *
#  "License"); you may not use this file except in compliance   *
#  with the License.  You may obtain a copy of the License at   *
#                                                               *
#    http://www.apache.org/licenses/LICENSE-2.0                 *
#                                                               *
#  Unless required by applicable law or agreed to in writing,   *
#  software distributed under the License is distributed on an  *
#  "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY       *
#  KIND, either express or implied.  See the License for the    *
#  specific language governing permissions and limitations      *
#  under the License.                                           *

FROM python:3.6-slim

# install deps
RUN apt-get update -y && apt-get install -y \
    libczmq-dev \
    libssl-dev \
    inetutils-telnet \
    bind9utils \
    gcc \
    && apt-get clean

RUN pip install --upgrade pip

RUN pip install apache-airflow==1.10.14
RUN pip install 'apache-airflow[kubernetes]'
RUN pip install 'apache-airflow[postgres]'


RUN cd /usr/local && mkdir airflow && chmod +x airflow && cd airflow

ARG AIRFLOW_USER_HOME=/usr/local/airflow
ENV AIRFLOW_HOME=${AIRFLOW_USER_HOME}

RUN useradd -ms /bin/bash 1000
RUN usermod -a -G sudo 1000

COPY airflow-test-env-init.sh /tmp/airflow-test-env-init.sh

RUN mkdir -p /usr/local/airflow/dags
RUN mkdir -p /usr/local/airflow/logs/scheduler

RUN cp -R /usr/local/lib/python3.6/site-packages/airflow/example_dags/* /usr/local/airflow/dags/ && \
    cp -R /usr/local/lib/python3.6/site-packages/airflow/contrib/example_dags/example_kubernetes_*.py /usr/local/airflow/dags/ && \
    cp -a /usr/local/lib/python3.6/site-packages/airflow/contrib/example_dags/libs /usr/local/airflow/dags/


RUN chown -R 1000:1000 /usr/local
RUN chmod -R 777 /usr/local

COPY bootstrap.sh /bootstrap.sh
RUN chmod +x /bootstrap.sh
USER 1000 
ENTRYPOINT ["/bootstrap.sh"]

#Python Package Dependencies for Airflow
RUN pip install SQLAlchemy==1.3.23
RUN pip install Flask-SQLAlchemy==2.4.4