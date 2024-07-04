FROM python:latest as builder
WORKDIR /app
COPY app.py /app
COPY requirements.txt /app
RUN pip install --user --no-cache-dir -r requirements.txt

FROM python:latest
WORKDIR /app
COPY --from=builder /root/.local /root/.local
ENV PATH=/root/.local/bin:$PATH
COPY --from=builder /app /app
EXPOSE 3000
CMD ["python", "app.py"]
