# 📦 ETL MinIO → Parquet z Airflowem i Docker Compose

Zrobiłem prosty pipeline, który przetwarza pliki CSV wrzucane do MinIO. Airflow co minutę sprawdza folder `input/`, konwertuje nowe pliki do Parquet i zapisuje je do `output/`.

Całość działa lokalnie w Dockerze – wystarczy `docker-compose up` i wszystko się odpala (MinIO + Airflow). Bez kombinowania.

---

## 🔧 Jak działa pipeline

1. Do MinIO wrzucam pliki CSV do `bucket1/input/`
2. Airflow co minutę uruchamia DAG
3. DAG sprawdza, które pliki już przetworzył (trzyma to jako zmienna w Airflowie)
4. Każdy nowy CSV:
   - jest pobierany lokalnie,
   - konwertowany do `.parquet`,
   - wrzucany do `bucket1/output/`

---

## 🐳 Docker Compose

Wszystko jest opisane w `docker-compose.yml`. Odpala:
- kontener z MinIO (porty 9000 i 9001),
- kontener z Airflowem (port 8080), z `pyspark` i `boto3` doinstalowanymi z poziomu `AIRFLOW__CORE__EXECUTOR`.

Nie trzeba niczego instalować lokalnie poza Dockerem.

---

## 🔁 Uruchamianie

```bash
git clone https://github.com/twoj-login/minio-etl-airflow.git
cd minio-etl-airflow
docker-compose up

Po uruchomieniu:

    Airflow dostępny pod: http://localhost:8080 (login: airflow, hasło: airflow)

    MinIO: http://localhost:9001 (login: minioadmin, hasło: minioadmin)

📁 Struktura repo

.
├── dags/
│   └── minio_etl_dag.py      # DAG przetwarzający nowe CSV
├── docker-compose.yml        # MinIO + Airflow
├── .gitignore
└── README.md                 # Ten plik

🪣 Przykład struktury bucketa

Bucket: bucket1

input/
  ├── file1.csv
  └── file2.csv

output/
  ├── file1.parquet
  └── file2.parquet

✍️ Uwagi

Ten projekt zrobiłem głównie dla siebie, żeby poćwiczyć Airflowa i ogarnąć pipeline’y z S3-kompatybilnym storage’em. Wyszło prosto, ale działa.

Jak ktoś chce wrzucać pliki i mieć je automatycznie przerobione na Parquet – to wystarczy wrzucić do input/ i poczekać minutę.
