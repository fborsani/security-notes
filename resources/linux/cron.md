# Cron

## Positions

From left to right when printed by crontab:

| Field name       | Chars allowed   |
| ---------------- | --------------- |
| Minutes          | 0-59            |
| Hours            | 0-23            |
| Day of the month | 1-31            |
| Month            | 1-12 or Jan-Dec |
| Day of the week  | 0-7 or Mon-Sun  |

## Special characters

| Char  | Meaning           | Example               | Result                                            |
| ----- | ----------------- | --------------------- | ------------------------------------------------- |
| \*    | Any value         | 10 \* \* \* \*        | XX:10                                             |
| -     | Range             | 10 12-14 \* \* \*     | 12:10 13:10 14:10                                 |
| ,     | List values       | 10 6,12,15 \* \* \*   | 06:10 12:10 15:10                                 |
| /\<n> | Every nth element | \*/2 10-20/6 \* \* \* | Every 2 minutes in every 6 hours from 10AM to 8PM |

