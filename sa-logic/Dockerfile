FROM python:3.6.6-alpine

COPY sa /app
WORKDIR /app

RUN pip3 install -r requirements.txt && \
    python3 -m textblob.download_corpora

ENTRYPOINT ["python3"]
CMD ["sentiment_analysis.py"]

EXPOSE 5000
