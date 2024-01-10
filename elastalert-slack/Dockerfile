FROM python:3.7-slim

RUN apt-get update && \
    apt-get install -y gcc libffi-dev && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /usr/src/app

COPY requirements.txt ./

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["elastalert", "--verbose"]