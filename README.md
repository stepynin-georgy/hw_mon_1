# Домашнее задание к занятию "13.Системы мониторинга"

## Обязательные задания

1. Вас пригласили настроить мониторинг на проект. На онбординге вам рассказали, что проект представляет из себя 
платформу для вычислений с выдачей текстовых отчетов, которые сохраняются на диск. Взаимодействие с платформой 
осуществляется по протоколу http. Также вам отметили, что вычисления загружают ЦПУ. Какой минимальный набор метрик вы
выведите в мониторинг и почему?

- Мониторинг RAM
- Нагрузка CPU, Load Average
- Нагрузка на диск (скорость записи/чтения, inodes, используесое дисковое пространство)
- Показатели HTTP-запросов (количесвто запросов, скорость запроса, частота ошибок)

#
2. Менеджер продукта посмотрев на ваши метрики сказал, что ему непонятно что такое RAM/inodes/CPUla. Также он сказал, 
что хочет понимать, насколько мы выполняем свои обязанности перед клиентами и какое качество обслуживания. Что вы 
можете ему предложить?

RAM - утилизация оперативной памяти. inodes - файловые дескрипторы. Проще говоря, 1 inode = 1 файлу в системе. Даже если на жестком диске много свободного места, но inodes достигли максимального значения, то новые файлы не смогут записываться на систему. CPUla (или же Load Average) -  среднее значение нагрузки системы за разное время (1, 5, 15 минут).

Такие технические показатели более полезны для системных администраторов, инженеров. Однако менеджеров и клиентов больше волнуют конечные результаты работы сервиса, а не технические показатели. Чтобы понять, отвечает ли сервис потребностям клиента, необходимо установить дополнительные показатели, например:

- Максимально допустимое время простоя: Допустимый период времени, в течение которого сервис недоступен.
- Допустимый процент ошибок при подготовке отчетов: Допустимый процент ошибок, которые могут возникнуть при создании отчетов.
- Среднее время ответа на подготовку отчета: Среднее время, необходимое службе для ответа отчетом

#
3. Вашей DevOps команде в этом году не выделили финансирование на построение системы сбора логов. Разработчики в свою 
очередь хотят видеть все ошибки, которые выдают их приложения. Какое решение вы можете предпринять в этой ситуации, 
чтобы разработчики получали ошибки приложения?

Можно настроить решения для мониторинга с открытым исходным кодом, такие как Zabbix, Prometheus, Elastic Stack, TICK Stack и т.д.

#
4. Вы, как опытный SRE, сделали мониторинг, куда вывели отображения выполнения SLA=99% по http кодам ответов. 
Вычисляете этот параметр по следующей формуле: summ_2xx_requests/summ_all_requests. Данный параметр не поднимается выше 
70%, но при этом в вашей системе нет кодов ответа 5xx и 4xx. Где у вас ошибка?


Формула `summ_2xx_requests/summ_all_requests` учитывает только коды состояния 2xx. Однако HTTP-запросы также могут заканчиваться другими кодами "без ошибок", которые необходимо учитывать, такими как:

- Информационные коды: 100-199
- Коды перенаправления: 300-399

#
5. Опишите основные плюсы и минусы pull и push систем мониторинга.

Push-системы мониторинга:

Плюсы:

1. Меньшая задержка времени: данные передаются в режиме реального времени, система мониторинга также отображает информацию почти в реальном времени. Это особенно важно для критически важных систем, где даже кратковременная задержка может иметь серьезные последствия.

2. Большая гибкость настройки: в push-системах мониторинга можно гибко настроить, выбирать какие данные и как часто отправлять.

Минусы:

1. Высокая нагрузка на сеть: поскольку данные активно на сервер мониторинга, то значительно вырастает нагрузка на сеть. Это может вызывать проблемы с пропускной способностью сети.

2. Настройка push-систем мониторинга может вызвать сложности при ее настройке, т.к. нужно будет определиться с метриками, которые нужно передавать на сервер мониторинга.

Pull-системы мониторинга:

Плюсы:

1. Меньшая нагрузка на сеть: в pull-системах мониторинга мониторинговая система запрашивает данные у мониторируемых систем только тогда, когда это необходимо. Это помогает снизить нагрузку на сеть.

2. Единая точка контроля: при использовании pull-системы мониторинга весь процесс мониторинга и получение данных осуществляется на сервере мониторинга. Это упрощает управление и обеспечивает единую точку контроля.

Минусы:

1. Задержка времени: в pull-системах мониторинга данные получаются только после того, как система мониторинга запросит их у мониторируемых устройств или приложений. Это может затруднить моментальную реакцию на проблемы и отсрочить их решение.

2. Отказоустойчивость: если сервер мониторинга недоступен или некорректно настроен, данные могут быть недоступны или неполными.

#
6. Какие из ниже перечисленных систем относятся к push модели, а какие к pull? А может есть гибридные?

    - Prometheus - использует pull модель, получает данные с экспортеров, которые установлены на мониторируемые хосты 
    - TICK - скорее всего push модель, т.к. Telegraf активно собирает метрики и отправляет их в InfluxDB.
    - Zabbix - использует гибридную модель, т.к. он принимает данные от своих агентов. Но при этом и отправляет запросы своим агентам, чтобы проверить их доступность либо доступность оборудования.
    - VictoriaMetrics - использует гибридную модель. Он может принимать данные с экспортеров или запрашивать данные из других систем мониторинга.
    - Nagios - использует гибридную модель, pull модель используется по умолчанию, но может быть настроен push режим работы.

#
7. Склонируйте себе [репозиторий](https://github.com/influxdata/sandbox/tree/master) и запустите TICK-стэк, 
используя технологии docker и docker-compose.

В виде решения на это упражнение приведите скриншот веб-интерфейса ПО chronograf (`http://localhost:8888`). 

![изображение](https://github.com/stepynin-georgy/hw_mon_1/blob/main/img/Screenshot_89.png)

P.S.: если при запуске некоторые контейнеры будут падать с ошибкой - проставьте им режим `Z`, например
`./data:/var/lib:Z`
#
8. Перейдите в веб-интерфейс Chronograf (http://localhost:8888) и откройте вкладку Data explorer.
        
    - Нажмите на кнопку Add a query
    - Изучите вывод интерфейса и выберите БД telegraf.autogen
    - В `measurments` выберите cpu->host->telegraf-getting-started, а в `fields` выберите usage_system. Внизу появится график утилизации cpu.
    - Вверху вы можете увидеть запрос, аналогичный SQL-синтаксису. Поэкспериментируйте с запросом, попробуйте изменить группировку и интервал наблюдений.

![изображение](https://github.com/stepynin-georgy/hw_mon_1/blob/main/img/Screenshot_90.png)

Для выполнения задания приведите скриншот с отображением метрик утилизации cpu из веб-интерфейса.
#
9. Изучите список [telegraf inputs](https://github.com/influxdata/telegraf/tree/master/plugins/inputs). 
Добавьте в конфигурацию telegraf следующий плагин - [docker](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/docker):
```
[[inputs.docker]]
  endpoint = "unix:///var/run/docker.sock"
```

Дополнительно вам может потребоваться донастройка контейнера telegraf в `docker-compose.yml` дополнительного volume и 
режима privileged:
```
  telegraf:
    image: telegraf:1.4.0
    privileged: true
    volumes:
      - ./etc/telegraf.conf:/etc/telegraf/telegraf.conf:Z
      - /var/run/docker.sock:/var/run/docker.sock:Z
    links:
      - influxdb
    ports:
      - "8092:8092/udp"
      - "8094:8094"
      - "8125:8125/udp"
```

После настройке перезапустите telegraf, обновите веб интерфейс и приведите скриншотом список `measurments` в 
веб-интерфейсе базы telegraf.autogen . Там должны появиться метрики, связанные с docker.

![изображение](https://github.com/stepynin-georgy/hw_mon_1/blob/main/img/Screenshot_91.png)

Факультативно можете изучить какие метрики собирает telegraf после выполнения данного задания.

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

1. Вы устроились на работу в стартап. На данный момент у вас нет возможности развернуть полноценную систему 
мониторинга, и вы решили самостоятельно написать простой python3-скрипт для сбора основных метрик сервера. Вы, как 
опытный системный-администратор, знаете, что системная информация сервера лежит в директории `/proc`. 
Также, вы знаете, что в системе Linux есть  планировщик задач cron, который может запускать задачи по расписанию.

Суммировав все, вы спроектировали приложение, которое:
- является python3 скриптом
- собирает метрики из папки `/proc`
- складывает метрики в файл 'YY-MM-DD-awesome-monitoring.log' в директорию /var/log 
(YY - год, MM - месяц, DD - день)
- каждый сбор метрик складывается в виде json-строки, в виде:
  + timestamp (временная метка, int, unixtimestamp)
  + metric_1 (метрика 1)
  + metric_2 (метрика 2)
  
     ...
     
  + metric_N (метрика N)
  
- сбор метрик происходит каждую 1 минуту по cron-расписанию

Для успешного выполнения задания нужно привести:

а) работающий код python3-скрипта,

б) конфигурацию cron-расписания,

в) пример верно сформированного 'YY-MM-DD-awesome-monitoring.log', имеющий не менее 5 записей,

P.S.: количество собираемых метрик должно быть не менее 4-х.
P.P.S.: по желанию можно себя не ограничивать только сбором метрик из `/proc`.

2. В веб-интерфейсе откройте вкладку `Dashboards`. Попробуйте создать свой dashboard с отображением:

    - утилизации ЦПУ
    - количества использованного RAM
    - утилизации пространства на дисках
    - количество поднятых контейнеров
    - аптайм
    - ...
    - фантазируйте)
    
    ---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---

