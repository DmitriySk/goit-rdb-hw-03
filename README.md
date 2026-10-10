# goit-rdb-hw-03 — NYC TLC Yellow Taxi у PostgreSQL

## Датасет

- **Назва:** NYC TLC Yellow Taxi Trip Records, липень 2026 + довідник зон Taxi Zone Lookup.
- **Офіційне джерело:** https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page
  - поїздки: https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2026-07.parquet (3 530 109 рядків)
  - зони: https://d37ci6vzurychx.cloudfront.net/misc/taxi_zone_lookup.csv (265 рядків)

## Формування вибірки

1. Ноутбук сам завантажує місячний Parquet і довідник зон (весь рік не завантажується).
2. З Parquet беруться 21 колонка і **100 000** випадкових рядків
   (`pandas.DataFrame.sample(n=100_000, random_state=42)`, у порядку файлу).
3. Вибірка зберігається в CSV `yellow_tripdata_2026-07_sample.csv` і завантажується в PostgreSQL через `COPY FROM STDIN`.

| таблиця | рядків |
|---|---|
| `hw3_taxi_staging` (сирі поїздки, `TEXT`) | 100 000 |
| `hw3_taxi_trips` (типізовані поїздки) | 100 000 |
| `hw3_taxi_zones_staging` / `hw3_taxi_zones` | 265 |

## Запуск

Відкрийте `dz_topic_3_Skalskyi.ipynb` у Google Colab і виконайте **Runtime → Restart and run all**.
PostgreSQL запускається всередині ноутбука через `pgserver`; залежності ставить перша кодова клітинка.

## Структура ноутбука

1. Setup і завантаження: pgserver, Parquet → CSV, staging-таблиці, `COPY FROM STDIN`.
2. Staging і DDL: типізована таблиця з PK, `NOT NULL`, `CHECK`, FK; обґрунтування типів колонок.
3. Data-quality audit: кількість рядків, дублікати, null-rate і спеціальні маркери, прапорці якості.
4. Розподіли та аномалії: категорії, числова статистика, правило 3σ та доменна перевірка.
5. 12 DQL/EDA-запитів у форматі Business-question → SQL → Interpretation.
6. Reflection.
