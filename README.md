# Anyview Bot

Бот для Windows обходит задания по C в Anyview, запрашивает решения у Gemini и проверяет их в редакторе курса.

## Запуск

Требуются Python 3.10+, Chrome и доступ к Anyview.

```powershell
python -m pip install -r requirements.txt
python -X utf8 .\anyview_bot.py
```

Для просмотра одной задачи без Gemini и отправки решения запусти `python -X utf8 .\anyview_bot.py --inspect --limit 1`. Параметр `--task` принимает код конкретной задачи.

## Gemini с другого компьютера

По умолчанию бот обращается к HTTPS-прокси, адрес которого указан в скрипте. SSH и дополнительные настройки сети не нужны. При первом запуске введи в терминале тот же ключ Gemini, который был настроен для прокси. Доступ привязан к этому ключу. Ключ вводится скрыто и сохраняется локально с шифрованием Windows.

## Дополнительный режим через SSH

Открой SSH-туннель в отдельном терминале. Подставь логин и адрес своего сервера с доступом к Gemini API.

```powershell
ssh -N -D 127.0.0.1:1085 -o ExitOnForwardFailure=yes user@server
```

В другом окне проверь маршрут. Ответ HTTP 401 или 403 без ключа подтверждает, что Google доступен через туннель.

```powershell
curl.exe --noproxy "" --proxy socks5h://127.0.0.1:1085 --max-time 20 -sS -o NUL -w "%{http_code}" https://generativelanguage.googleapis.com/v1beta/models
```

Затем запусти бота из этого же окна.

```powershell
$env:GEMINI_SOCKS_PROXY = 'socks5h://127.0.0.1:1085'
python -X utf8 .\anyview_bot.py
```

Туннель использует локальный порт. Через него идут только запросы бота к Gemini по HTTPS, включая разрешение DNS на SSH-сервере. Chrome продолжает открывать Anyview обычным способом. Если туннель прервётся, запросы Gemini завершатся ошибкой. Чтобы вернуться к встроенному HTTPS-прокси, удали переменную `GEMINI_SOCKS_PROXY` из текущего терминала.

Ключ Gemini и учётные данные Anyview хранятся локально с шифрованием Windows. Профиль Chrome, журналы и исходники решений по умолчанию лежат в `anyview-data/`.
