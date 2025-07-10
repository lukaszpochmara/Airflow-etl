# CSV do Parquet z Airflow i MinIO (Docker)

Mały projekt do nauki pipeline’ów danych. Chciałem zbudować coś prostego, co:

- sprawdza co 5 minut, czy na MinIO pojawił się nowy plik CSV,
- jeśli tak – konwertuje go do Parquet,
- i wrzuca z powrotem do MinIO do innego folderu.

Użyłem Airflow do zarządzania zadaniami, MinIO jako S3-kompatybilnego storage’u i Docker Compose, żeby wszystko odpalić lokalnie.

---

## Jak to działa

1. Do MinIO wrzucam plik: `bucket1/input/hw_200.csv`
2. Airflow uruchamia DAG co 5 minut
3. Sprawdzany jest hash pliku (md5) – jak inny niż poprzedni, to:
   - plik jest pobierany,
   - konwertowany do Parquet (`/tmp/hw_200.parquet`)
   - i wrzucany do: `bucket1/output/hw_200.parquet`

---

## Uruchomienie

1. Klonujesz repo:
```bash
git clone https://github.com/twoj-login/etl-csv-parquet-minio.git
cd etl-csv-parquet-minio

    Odpalasz dockera:

docker-compose up

    Wchodzisz w GUI:

    Airflow: http://localhost:8080 (airflow / airflow)

    MinIO: http://localhost:9001 (minioadmin / minioadmin)

Wymagania

Nie trzeba nic instalować – wszystko siedzi w Dockerze. Wymagane tylko:

    Docker

    Docker Compose

Pliki w repo

.
├── dags/                       # DAG z Airflowa
│   └── csv_to_parquet_dag.py
├── docker-compose.yml         # Airflow + MinIO
├── diagram.png                # prosty rysunek jak to działa
└── README.md
