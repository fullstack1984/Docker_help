# Команды для управления контейнерами

**Контейнер (Docker Container)** — это исполняемый экземпляр запущенного образа. Можно создать, запустить, остановить, переместить или удалить контейнер с помощью DockerAPI или CLI. Контейнер можно запускать на локальных машинах, виртуальных машинах или развертывать в облаке. Контейнер может работать на любой ОС. Контейнер изолирован от других контейнеров и запускает собственное программное обеспечение, двоичные файлы и конфигурации.

Команды:

* create — создает контейнер из выбранного образа
* start — активирует уже существующий контейнер
* run — создает новый контейнер и сразу включает его
* ls — отображает все существующие контейнеры
* inspect — подробнее рассказывает о выбранном контейнере
* logs — выводит в консоль логи (журнал событий)
* pause — приостанавливает все запущенные процессы внутри контейнера
* stop — пытается остановить выбранный контейнер, отправив ему сигнал SIGTERM, требующий завершить всю активность и сохранить пользовательские данные. Если ответ занимает слишком много времени, то следом посылает сигнал SIGKILL, чтобы «убить» процесс без сохранения данных
* kill — выполняет ту же задачу, что и предыдущая команда, но пропускает шаг с отправкой SIGTERM. Сразу выключает контейнер, игнорируя сохранение пользовательских данных
* rm — удаляет выбранный контейнер (он должен быть выключен, чтобы команда сработала)

Рассмотрим каждую из команд подробнее.

## create

Из названия понятно, что речь идет о создании нового контейнера. Это основная функция Docker, с нее начинается его работа.

Синтаксис: 

docker create [опции] название образа [дополнительные команды][аргументы]
В квадратных скобках указываются опциональные компоненты команды. Использовать их необязательно, но можно оптимизировать создаваемый контейнер под свои задачи. 
К примеру, добавить ссылку на другой контейнер с помощью тега –link. Или подключить Standard Input с помощью команды, наподобие этой:

> docker create -a STDIN название образа

Либо создать контейнер из образа операционной системы Ubuntu, добавив подключение к терминалу и запуск bash при каждом старте Docker: 

> docker create -t -I ubuntu bash

Только при наличии терминала можно управлять контейнером. Поэтому его нужно обязательно запрашивать при каждому запуске. 
Рассмотрим пример, где команда docker create создаст новый контейнер с использованием последнего образа Fedora.
Перед созданием контейнера команда проверит, доступен ли на хосте последний официальный образ Fedora. Если нет, то прежде чем создать контейнер, она скачает необходимый образ из Docker Hub. Если же образ Fedora уже есть на хосте, команда использует его и создаст контейнер.
После успешного создания контейнера Docker вернет его ID. Например, в указанном примере 4d598b0ba8301d8e3cba2a67230d37a4fc9a7dc33629e6fc31bd2fab78c88aa8 - это ID контейнера, возвращенный Docker.
Каждый контейнер имеет уникальный ID. С его помощью мы обращаемся к контейнеру при осуществлении разнообразных операций, таких как запуск, остановка, перезапуск и так далее.
Рассмотрим другой пример использования команды docker create, с опциями и передаваемыми командами:
В этом примере команда docker create создает контейнер, используя образ Ubuntu. Как уже говорилось, если на хосте нужного образа не окажется, он будет скачан из Docker Hub.
Опции -t и -i говорят Docker назначить контейнеру терминал, чтобы пользователь мог взаимодействовать с контейнером. Также Docker получает инструкцию выполнять команду bash при каждом старте.

## start

В отличие от предыдущей команды, эта запускает контейнер, который существует, но находится в нерабочем состоянии:

> docker start [опции] название или ID контейнера [название или ID контейнера]

Все просто: вводите команду, а потом указываете название контейнера, чтобы его запустить:

> docker start 49920 — пример старта через ID контейнера 49920<br>
> docker start testing_docker — пример старта через название testing_docker

Какой из вариантов использовать, решает разработчик, ориентируясь на собственные предпочтения. Технических отличий нет.

## stop

Выполняет противоположную функцию. То есть не запускает контейнер, а, наоборот, останавливает его работу: 

> docker stop [опции] название или ID контейнера [название или ID контейнера]

Например, чтобы выключить контейнер с именем Timeweb, надо ввести в терминал:

> docker stop timeweb

Аналогично с ID. Просто имя надо заменить на уникальный код, привязанный к контейнеру. Получится примерно так:

> docker stop 14490

## restart

Последняя команда, связанная с рабочим состоянием. Она выполняет перезапуск выбранного контейнера.

Синтаксис следующий: 

> docker restart [опции] название или ID контейнера [название или ID контейнера]

Вводите команду, а потом указываете название контейнера, чтобы его перезапустить:

> docker restart 49920 — пример перезапуска через ID контейнера 49920<br>
> docker restart testing_timeweb — пример перезапуска через название testing_timeweb

Если ID контейнера представляет собой длинную строку, проще использовать название контейнера: 

## container ls (или docker ps)

Обе команды выполняют одну и ту же задачу. Просто одна из них использовалась раньше, а другая появилась позднее. 
docker ps отображает в терминале все запущенные контейнеры. А при добавлении опции -a в список попадают все контейнеры, созданные в системе.

В вывод команды попадают следующие параметры:

* ID — то есть кодовое значение конкретного контейнера.
* IMAGE — образ, используемый контейнером.
* COMMAND — список команд, которые должны выполняться сразу после запуска.
* CREATED — статус и время, когда контейнер бы создан (допустим, «35 минут назад»).
* STATUS — текущее состояние – включен ли и время работы с последнего запуска. Тут может отображаться код выхода и время, когда он произошел.
* PORTS — порты, используемые контейнером из списка.
* NAMES — более понятные в сравнении с ID имена, которые можно точно так же использовать для исполнения команд или поиска.

При желании можно использовать опцию volume, чтобы в терминале отображались только тома, являющиеся предпочтительным механизмом хранения данных в Docker.
По умолчанию столбцы с длиной, превышающей указанный предел, усекаются. Используйте опцию --no-trunc, чтобы отключить усечение:

> docker container ls --no-trunc

Чтобы отображались только идентификаторы контейнеров, можно использовать опцию -q, --quiet:

> docker container ls -q

--format позволяет форматировать вывод. Например, чтобы напечатать только имена и статус контейнеров, включая заголовок, который вы запустите:

> docker container ls --format 'table {{.Names}}\t{{.Status}}'

-s, --size используется для просмотра размера контейнеров. Каждая строка будет содержать столбец с именем SIZE, который показывает размер контейнера:

> docker container ls -s

Опция --last, -n указывает команде отображать n последних созданных контейнеров, включая все состояния. Например, для просмотра последних двух созданных контейнеров нужно запустить:

> docker container ls -n 2

Существует также возможность перечислить только последний созданный контейнер --latest, -l, который совпадает с -n 1:

> docker container ls -l

Опция --filter, -f позволяет вам фильтровать выходные данные на основе определенных критериев. Например, чтобы просмотреть только контейнеры со статусом exited, вы должны запустить:

> docker container ls -f "status=exited"

## logs

Одна из команд, позволяющая подробнее ознакомиться с тем, как работает Docker. Точнее одна из его запущенных единиц. Показывает, как функционирует контейнер и что в текущий момент с ним происходит. Выводит некий журнал данных. 
Если указать ключ –follow и название контейнера, то в терминал в реальном времени будут выводиться логи Docker. Так можно беспрерывно наблюдать за его работой и возможным появлением ошибок:

> docker logs --follow 458f659d1ee6

У команды logs также существует опция –tail, которая позволяет ограничить количество строк лога контейнера, выводимых на экран:

> docker logs --tail <количество строк> <container_id>

## inspect 

Отображает подробную информацию из Docker после обращения к конкретному контейнеру.

Синтаксис следующий: 

> docker inspect [опции] название или ID контейнера [название или ID контейнера]

По умолчанию отображает данные в формате JSON. Можно использовать дополнительную опцию –format для смены формата данных.

> docker inspect –format=‘{{range .NetworkSettings.Networks}}{{.MacAddress}}{{end}}’ $INSTANCE_ID — пример вывода информации о MAC-адресе

> docker inspect –format=‘{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}’ $INSTANCE_ID — пример вывода информации о портах

## rm

Команда удаления одного или нескольких контейнеров Docker, найденных на диске вашего хостинга. 

Синтаксис следующий: 

> docker rm [опции] название или ID контейнера [название или ID контейнера]

Чтобы удалить сразу несколько контейнеров с разными названиями и ID, нужно ввести:

> docker rm 45fs testing_timeweb

Перед тем как удалить контейнер, нужно его выключить. Для этого надо воспользоваться командой:

> docker stop