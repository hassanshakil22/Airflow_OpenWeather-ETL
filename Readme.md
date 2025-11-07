

# Airflow OpenWeather ETL Pipeline

A **Dockerized ETL pipeline** using **Apache Airflow** that extracts weather data from the **OpenWeather API**, transforms it, and loads it into **PostgreSQL**. The project is deployed on an **AWS EC2 instance** and optionally stores historical CSV data in **S3**.

---

## Features

- Extracts real-time weather data for specified cities from OpenWeather API.  
- Transforms temperatures from Kelvin to Fahrenheit and formats timestamps.  
- Loads transformed data into PostgreSQL (`weather_data` table).  
- Optional storage of CSV files in S3 for historical data.  
- Fully Dockerized setup using Airflow, PostgreSQL, and PgAdmin.  
- Local port forwarding for secure access to Airflow UI, PgAdmin, and Postgres.  

---

## Tech Stack

- **Apache Airflow** – Workflow orchestration and scheduling  
- **Python** – ETL scripts with Pandas  
- **PostgreSQL** – Relational database for storing weather data  
- **AWS EC2** – Hosting the Dockerized environment  
- **Docker & Docker Compose** – Containerization  
- **S3** (optional) – Storage for historical CSVs  

---

## Getting Started

### Prerequisites

- AWS account & EC2 instance (`t2.xlarge` recommended)  
- Docker & Docker Compose installed  
- OpenWeatherMap API key  
- Optional: AWS CLI configured for S3 access  

---

### Clone the Repo

```bash
git clone https://github.com/<your-username>/Airflow-OpenWeather-Pipeline.git
cd Airflow-OpenWeather-Pipeline
Setup Environment
Create .env file (for Airflow UID):

bash
Copy code
echo "AIRFLOW_UID=$(id -u)" > .env
Start Docker containers:

bash
Copy code
docker-compose up -d
Verify containers are running:

bash
Copy code
docker ps
Access Services Locally
Use SSH with port forwarding:

bash
Copy code
ssh -i "dependency.pem" ec2-user@<EC2-PUBLIC-DNS> \
-L 8080:localhost:8080 \
-L 5050:localhost:5050 \
-L 5433:localhost:5433 \
-L 5555:localhost:5555
Then access in your browser:

Airflow UI: http://localhost:8080

PgAdmin: http://localhost:5050

PostgreSQL: Local clients can connect to localhost:5433

Airflow Connections
weathermap_api – HTTP connection to OpenWeather API

postgres_default – PostgreSQL connection to local database

PostgreSQL Table
sql
Copy code
CREATE TABLE IF NOT EXISTS weather_data (
    id SERIAL PRIMARY KEY,
    city VARCHAR(50),
    description VARCHAR(100),
    temperature_f FLOAT,
    feels_like_f FLOAT,
    min_temp_f FLOAT,
    max_temp_f FLOAT,
    pressure INTEGER,
    humidity INTEGER,
    wind_speed FLOAT,
    time_of_record TIMESTAMP,
    sunrise TIMESTAMP,
    sunset TIMESTAMP
);
Running the DAG
DAG Name: weather_dag

Schedule: @daily

Tasks:

is_weather_api_ready – Checks API availability

extract_weather_data – Extracts JSON from OpenWeather

transform_load_weather_data – Transforms & loads data into PostgreSQL (and optionally S3)

Optional: Save to S3
Set AWS credentials in your DAG and the transformed data will be stored as CSV in an S3 bucket.

Folder Structure
bash
Copy code
Airflow-OpenWeather-Pipeline/
├── dags/                # Airflow DAGs
│   └── weather_dag.py
├── docker-compose.yml   # Docker Compose config
├── .env                 # Environment variables for Airflow UID
├── README.md
└── requirements.txt     # Python dependencies (if any)
