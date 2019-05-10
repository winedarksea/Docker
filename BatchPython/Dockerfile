
FROM python:3.6.8-stretch

WORKDIR /app

COPY . /app

RUN apt-get -y update  && apt-get install -y \
  python3-dev \
  apt-utils \
  apt-transport-https \
  python-dev \
  build-essential \
  curl \
  libpq-dev \
  swig \
&& rm -rf /var/lib/apt/lists/* \
&& apt-get clean -y
# libpq-dev is postgres

# Microsoft ODBC Driver Install for Debian 9 (Stretch)
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
RUN curl https://packages.microsoft.com/config/debian/9/prod.list > /etc/apt/sources.list.d/mssql-release.list
RUN apt-get update
RUN ACCEPT_EULA=Y apt-get install -y msodbcsql17

# FreeTDS Open-Source Driver
RUN apt-get install -y unixodbc unixodbc-dev tdsodbc libssl-dev \
&& apt-get clean -y

# FreeTDS Configuration
RUN echo "[FreeTDS]\n\
Description = FreeTDS unixODBC Driver\n\
Driver = /usr/lib/x86_64-linux-gnu/odbc/libtdsodbc.so\n\
Setup = /usr/lib/x86_64-linux-gnu/odbc/libtdsS.so"  >> /etc/odbcinst.ini
# /usr/lib/arm-linux-gnueabi/odbc/libtdsodbc.so\n\   # dpkg --search libtdsodbc.so
# /usr/lib/arm-linux-gnueabi/odbc/libtdsS.so" >> /etc/odbcinst.ini 
# dpkg --search libtdsS.so or maybe just: sudo dpkg-reconfigure tdsodbc



# not a pretty as a requirements.txt, but quick to follow
RUN pip install --upgrade pip
RUN pip install --upgrade setuptools
RUN pip install six
RUN pip install cython
RUN pip install numpy>=1.9.0
RUN pip install numba
# Web Scrapping and NLP
RUN pip install requests
RUN pip install twisted
RUN pip install lxml
RUN pip install pyOpenSSL
RUN pip install gensim
RUN pip install nltk
RUN pip install Scrapy
RUN pip install geopy geocoder
# Basic Data Science
RUN pip install scipy>=0.14.1
RUN pip install matplotlib
RUN pip install scikit-learn
RUN pip install statsmodels
RUN pip install xgboost>=0.80
# Dedupe
RUN pip install recordlinkage
RUN pip install dedupe
# Data Connections
RUN pip install sqlalchemy
RUN pip install pyodbc
RUN pip install azure # consider just pip install azure.storage.blob 
RUN pip install psycopg2 # postgres
# Advanced Modeling
RUN pip install tensorflow
RUN pip install keras
RUN curl https://raw.githubusercontent.com/automl/auto-sklearn/master/requirements.txt | xargs -n 1 -L 1 pip install # auto-sklearn reqs
RUN pip install auto-sklearn # models need to be trained in exact same version as version used on deployment
RUN pip install pystan
RUN pip install fbprophet


CMD ["python"]

# CMD [ "python", "./combinedScripts.py" ]
# docker build --tag=batchpython1 .
# docker run -v /home/USV_vm/vm-iaprod-ubuntu-1/Projects/Forecasting/DailyForecast:/projects batchpython1 python /projects/combinedInventoryDaily.py

# example taken from https://blog.realkinetic.com/building-minimal-docker-containers-for-python-applications-37d0272c52f3
# also this: https://github.com/lppier/docker-prophet/blob/master/Dockerfile#L10
