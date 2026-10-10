# goit-rdb-hw-03 — NYC TLC Yellow Taxi у PostgreSQL

Домашнє завдання до Теми 3: завантаження даних у PostgreSQL (staging → clean), аудит якості та EDA на SQL.

## Датасет

- **Назва:** NYC TLC Yellow Taxi Trip Records, липень 2026 + довідник зон Taxi Zone Lookup.
- **Офіційне джерело:** https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page
  - поїздки: https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2026-07.parquet (3 530 109 рядків)
  - зони: https://d37ci6vzurychx.cloudfront.net/misc/taxi_zone_lookup.csv (265 рядків)

## Формування вибірки

1. Ноутбук сам завантажує місячний Parquet і довідник зон у теку `data/` (весь рік не завантажується).
2. З Parquet беруться 21 колонка і **100 000** випадкових рядків
   (`pandas.DataFrame.sample(n=100_000, random_state=42)`, у порядку файлу).
3. Вибірка зберігається в `data/yellow_tripdata_2026-07_sample.csv` і завантажується в PostgreSQL через `COPY FROM STDIN`.

| таблиця | рядків |
|---|---|
| `hw3_taxi_staging` (сирі поїздки, `TEXT`) | 100 000 |
| `hw3_taxi_trips` (типізовані поїздки) | 100 000 |
| `hw3_taxi_zones_staging` / `hw3_taxi_zones` | 265 |

## Структура репозиторію

```
dz_topic_3_Skalskyi.ipynb               ноутбук з output клітинок
data/yellow_tripdata_2026-07_sample.csv вибірка 100 000 поїздок (≈ 11 МБ)
data/taxi_zone_lookup.csv               довідник зон
requirements.txt                        залежності для локального запуску
```

Parquet (≈ 62 МБ) у репозиторій не входить — ноутбук завантажує його з офіційного сайту.
Вибірка відтворювана: при повторному запуску з тим самим `random_state` CSV виходить таким самим.

## Запуск

**Google Colab:** відкрийте `dz_topic_3_Skalskyi.ipynb` і виконайте **Runtime → Restart and run all**.
Залежності ставить перша кодова клітинка, PostgreSQL запускається всередині ноутбука через `pgserver`.

**Локально:**

```bash
python -m venv .venv
.venv\Scripts\activate        # Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook dz_topic_3_Skalskyi.ipynb
```

Дані Postgres зберігаються в теці `pgdata/` (у git не входить). На Windows ноутбук сам докладає
tz-базу з пакета `tzdata`, бо збірка `pgserver` її не містить.

## Структура ноутбука

1. Setup і завантаження: pgserver, Parquet → CSV, staging-таблиці, `COPY FROM STDIN`.
2. Staging і DDL: типізована таблиця з PK, `NOT NULL`, `CHECK`, FK; обґрунтування типів колонок.
3. Data-quality audit: кількість рядків, дублікати, null-rate і спеціальні маркери, прапорці якості.
4. Розподіли та аномалії: розподіл `payment_type`, статистика `fare_amount`, правило 3σ та доменна перевірка.
5. 10 DQL/EDA-запитів у форматі Business-question → SQL → Interpretation.
6. Reflection.
