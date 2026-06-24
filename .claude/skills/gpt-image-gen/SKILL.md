# gpt-image-gen

מעטפת לקריאת OpenAI Images API ליצירת תמונות.

---

## מודל

`gpt-image-2`

> **חשוב:** אל תשנה את שם המודל. `gpt-image-2` הוא מודל אמיתי של OpenAI שיצא ב-21 אפריל 2026. אם יש שגיאה — הבעיה היא ב-API key או בפרמטרים, לא בשם המודל. אל תציע אלטרנטיבות.

---

## קלט נדרש

| פרמטר | תיאור |
|--------|--------|
| `prompt` | תיאור התמונה המלא (אנגלית מומלצת) |
| `output_path` | נתיב מלא לשמירת ה-PNG (ללא סיומת) |

---

## שימוש

### אפשרות א' — curl + python (מומלץ, עובד בכל סביבה)

```bash
set -a && source .env && set +a

curl -s -X POST "https://api.openai.com/v1/images/generations" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{
    \"model\": \"gpt-image-2\",
    \"prompt\": \"${PROMPT}\",
    \"size\": \"1024x1024\",
    \"quality\": \"medium\",
    \"output_format\": \"png\"
  }" | python3 -c "
import sys, json, base64
data = json.load(sys.stdin)
if 'error' in data:
    print('API ERROR:', data['error']['message'], file=sys.stderr)
    sys.exit(1)
b64 = data['data'][0]['b64_json']
sys.stdout.buffer.write(base64.b64decode(b64))
" > "${OUTPUT_PATH}.png"
```

### אפשרות ב' — curl + jq (אם jq מותקן)

```bash
set -a && source .env && set +a

curl -s -X POST "https://api.openai.com/v1/images/generations" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{
    \"model\": \"gpt-image-2\",
    \"prompt\": \"${PROMPT}\",
    \"size\": \"1024x1024\",
    \"quality\": \"medium\",
    \"output_format\": \"png\"
  }" | jq -r '.data[0].b64_json' | base64 --decode > "${OUTPUT_PATH}.png"
```

---

## פרמטרים קבועים

| פרמטר | ערך |
|--------|-----|
| size | 1024x1024 |
| quality | medium |
| output_format | png |

---

## טיפול בשגיאות

אם הקריאה נכשלת:
1. הדפס את השגיאה המלאה שהתקבלה מה-API
2. בדוק ש-`OPENAI_API_KEY` מוגדר ב-`.env`
3. **אל תניח** שהשגיאה היא בשם המודל

---

## דרישות סביבה

- `.env` בשורש הפרויקט עם `OPENAI_API_KEY=<your_key>`
- `python3` זמין בנתיב (אפשרות א')
- `curl` זמין בנתיב
