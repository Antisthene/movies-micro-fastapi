FROM python:3.8-slim

WORKDIR /app

COPY ./requirements.txt /app/requirements.txt

RUN apt-get update \
    && apt-get install gcc -y \
    && apt-get clean

RUN pip install -r /app/requirements.txt \
    && rm -rf /root/.cache/pip

COPY . /app/

EXPOSE 5001

CMD ["uvicorn", "app.main:app", "--reload","--host", "0.0.0.0", "--port", "5001"]
