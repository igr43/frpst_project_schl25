# Отключения коммунальных услуг во Владивостоке: подготовка данных

Кейс по аналитике данных: очистка и обогащение базы отключений электричества, воды и тепла во Владивостоке за 2018–2019 годы. Результат — единая чистая таблица, готовая для анализа и визуализации.

![Python](https://img.shields.io/badge/Python-3-3776AB?logo=python&logoColor=white)
![pandas](https://img.shields.io/badge/pandas-150458?logo=pandas&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-003B57?logo=sqlite&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-F37626?logo=jupyter&logoColor=white)

## Данные

Исходник — база SQLite с адресным справочником (`cities`, `districts`, `streets`, `buildings` и др.) и таблицами `blackouts` (отключения) и `blackouts_buildings` (какие дома затронуло каждое отключение).

**25 264 отключения** с января 2018 по декабрь 2019:

| Тип | Отключений |
|---|---:|
| Горячая вода | 9 339 |
| Холодная вода | 9 109 |
| Электричество | 4 745 |
| Отопление | 2 071 |

## Этапы обработки

1. **Даты.** Строковые `start_date` / `end_date` переведены в `datetime` с безопасной обработкой ошибок.
2. **Длительность.** Рассчитаны `duration_hours` и `duration_days`; записи с отрицательной длительностью удалены, дубликаты по `id` убраны.
3. **Источник.** Пропуски в `source` заполнены значением `initiator_name`; добавлен флаг `has_source`.
4. **Масштаб.** Через таблицу связей посчитано `affected_buildings_count` — сколько домов затронуло каждое отключение.
5. **Текст.** Поля `type`, `source`, `initiator_name` приведены к единому виду (обрезка пробелов, нижний регистр).

## Результат

[`cleaned_blackouts.csv`](cleaned_blackouts.csv):

| Колонка | Описание |
|---|---|
| `id` | идентификатор отключения |
| `start_date`, `end_date` | начало и конец |
| `description` | причина в свободной форме |
| `type` | `electricity`, `hot_water`, `cold_water`, `heat` |
| `initiator_name` | организация, проводившая работы |
| `source`, `has_source` | источник информации и флаг его наличия в исходных данных |
| `duration_hours`, `duration_days` | длительность |
| `affected_buildings_count` | число затронутых домов |

Медианная длительность отключения — около 4,7 часа.

## Запуск

```bash
pip install -r requirements.txt
jupyter notebook data_prep.ipynb
```

Исходная база в репозиторий не входит. Перед запуском укажите путь к своему файлу `.db` в первой ячейке ноутбука (`sqlite3.connect(...)`).

## Автор

Куценко Игорь — [@igr43](https://github.com/igr43)
