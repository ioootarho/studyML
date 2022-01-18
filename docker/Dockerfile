FROM python:3.8-buster

RUN mkdir /handson
COPY requirements.txt /usr/src/requirements.txt

RUN pip install --upgrade pip \
&& pip install -r /usr/src/requirements.txt

EXPOSE 8888

CMD ["python3"]