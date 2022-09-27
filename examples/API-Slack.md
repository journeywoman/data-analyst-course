Что, если мы хотим не скачивать файлик и отправлять по почте, а отправлять данные прямо из кода, например, в Slack в какой-то общий канал или в личку нашим коллегам?

Пока мы с помощью API только забирали данные с сайтов, но через API можно и отправлять данные на сайты.

Для работы с API Slack через Python есть специальная библиотека slackclient

Однако эта библиотека была подписана как LEGACY PYTHON SLACK SDK, то есть УСТАРЕВШАЯ  PYTHON БИБЛИОТЕКА. Еще надавно она была единственной и главной библиотекой.

Мы разберем, как работать с этой библиотекой и напишем несколько самостоятельных функций с помощью библитеки request.
```python
#после регистрации приложения в slack
kiki = 'xoxb-1831265609091-1827980937541-lD8WAqXLBkERwYn1juePNlvF' # наш токен
!pip install slackclient

# import sys
# !{sys.executable} -m pip install slackclient

from slack import WebClient # библиотека slackclient
from slack.errors import SlackApiError # обработчик ошибок slack

slack_token = kiki
client = WebClient(token=slack_token)

try:
    response = client.chat_postMessage(
    channel="slack-reporting-tools",
    text="Hello from your app! :tada:"
  )
except SlackApiError as e:
  # You will get a SlackApiError if "ok" is False
  assert e.response["error"]  # str like 'invalid_auth', 'channel_not_found'

#Например, нам нужно рассылать информацию о данных из Яндекс.Метрики

attendance_diff = attendance['visits'].pct_change().tail(1)


now = attendance['date'].max() # вытаскиваем последнюю дату, 
pct_change_str = str(round(attendance_diff.item(),3)*100)+'%' #

report = f"{now} посетителей на сайте {'больше' if attendance_diff.item() > 0 else 'меньше'} на {pct_change_str} чем вчера"

response = client.chat_postMessage(channel="slack-reporting-tools", # название канала
                                   text=report # текст сообщения
                                  )
#Мы хотим отправилять в сообщениях графики с посещаемостью

def post_file_to_slack(file_name, channel, file_bytes):
    method = 'https://slack.com/api/files.upload'
    params = {
        'token': kiki,
        'filename': file_name,
        'channels': channel, 
        }
    return requests.post(method, params, files = {'file': file_bytes }).json()

#Для начала сохраним на локальный компьютер изображение с помощью кода:

plot = attendance.set_index('date').plot(kind='bar',figsize=(15, 5)) # нарисуем график с помощью pandas
fig = plot.get_figure() #достанем фигуру
fig.savefig("barchart.png") # сохраним фигуру в виде картинки

with open('barchart.png', "rb") as image: #откроем файл с картинкой
    post_file_to_slack(file_name='График', 
                       channel='slack-reporting-tools',
                       file_bytes=image )  
```
