# Взаимодействие приложения с веб-сервером

### SAPI

SAPI (Server Application Programming Interface) -- программный интерфейс позволяющий чему-то либо(интерпретатору PHP например) работать в качестве части web-приложения (а по сути -- как часть веб-сервера), в частности -- получить конкретные запросы от программы-сервера и отдавать данные (определяемые уже внутренней логикой, которую реализовал на программист).

### CGI

**CGI (Common Gateway Interface)** — стандарт интерфейса, используемого для связи внешней программы с веб-сервером. Программу, которая работает по такому интерфейсу совместно с веб-сервером, принято называть шлюзом, хотя многие предпочитают названия «скрипт» (сценарий) или «CGI-программа». По сути позволяет использовать консоль ввода и вывода для взаимодействия с клиентом.

![](../media/cgi.jpeg)

Алгоритм работы:

1. Клиент запрашивает CGI-приложение по его URI.
2. Веб-сервер принимает запрос и устанавливает переменные окружения, через них приложению передаются данные и служебная информация.
3. Веб-сервер перенаправляет запросы через стандартный поток ввода (stdin) на вход вызываемой программы.
4. CGI-приложение выполняет все необходимые операции и формирует результаты в виде HTML.
5. Сформированный гипертекст возвращается веб-серверу через стандартный поток вывода (stdout). Сообщения об ошибках передаются через stderr.
6. Веб-сервер передает результаты запроса клиенту.

Недостатки CGI:

1. низкая производительность
2. каждое обращение к CGI-приложению вызывает порождение нового процесса
3. взаимодействуют с сервером через STDIN и STDOUT запущенного CGI-процесса
4. если приложение написано с ошибками, то возможна ситуация, когда оно, например, зациклится; браузер прервет соединение по истечении тайм-аута, но на серверной стороне процесс будет продолжаться, пока администратор не снимет его принудительно.
5. меньшая, по сравнению с другими решениями, защищенность веб-сервера
6. неправильная настройка прав доступа к серверным ресурсам из CGI-приложения может поставить под угрозу не только работоспособность веб-сервера, но и информационную безопасность

### FastCGI SAPI

Интерфейс FastCGI — клиент-серверный протокол взаимодействия веб-сервера и приложения, дальнейшее развитие технологии CGI. По сравнению с CGI является более производительным и безопасным.

1. PHP интерпретатор запускается как независимый сервер, обрабатывающий входящие запросы на исполнение PHP скриптов по протоколу FastCGI, что позволяет ему работать с любым веб-сервером, поддерживающим этот протокол
2. более производительный и безопасный
3. вместо того чтобы создавать новые процессы для каждого нового запроса, использует постоянно запущенные процессы для обработки множества запросов
4. использует Unix Domain Sockets или TCP/IP для связи с сервером
5. могут быть запущены не только на этом же сервере, но и где угодно в сети
6. возможна обработка запросов несколькими FastCGI-процессами, работающими параллельно
7. в кластере должен находиться только FastCGI-процесс, а не целый веб-сервер
8. обеспечивает дополнительную безопасность, такую как, например, запуск FastCGI-процесса под учётной записью пользователя, отличного от пользователя веб-сервера, а также может находиться в chroot'е, отличном от chroot'а веб-сервера
9. может быть использован в любом языке, поддерживающем сокеты.

### FPM SAPI

**FPM (FastCGI Process Manager), известный как php-fpm** — является альтернативной реализацией PHP FastCGI с несколькими дополнительными возможностями обычно используемыми для высоконагруженных сайтов.

- продвинутое управление процессами с корректной (graceful) процедурой остановки и запуска;
- возможность запуска воркеров с разными uid/gid/chroot/окружением, а также запуска на различных портах с использованием разных php.ini (замещение safe_mode)
- логирование стандартных потоков вывода (stdout) и ошибок (stderr)
- аварийный перезапуск в случае внезапного разрушения opcode-кеша
- поддержка ускоренной загрузки (accelerated upload)
- "slowlog" - логирование необычно медленно выполняющихся скриптов (не только их имена, но также и их трассировки. Это достигается с помощью ptrace и других подобных утилит для чтения данных исполнения удаленных процессов)
- fastcgi_finish_request() - специальная функция для завершения запроса и сброса всех буферов данных, причем процесс может продолжать выполнение каких-либо длительных действий
- динамическое/статическое порождение дочерних процессов
- базовая информация о статусе SAPI (аналогично Apache mod_status)
- конфигурационный файл, основанный на php.ini.
- с версии PHP 5.3.3, php-fpm был включён в PHP как отдельное SAPI

## CLI SAPI

**CLI SAPI** — в качестве скрипта командной строки, являющегося исполняемым файлом, который вызывается пользователем из командной строки.

- в отличие от CGI SAPI, заголовки не пишутся в поток вывода
- сообщения об ошибках выдаются в текстовом режиме
- PHP CLI не поддерживает GET, POST или загрузку файлов
- текущая директория не изменяется на рабочую директорию скрипта
- скрипт выполняется в окружении вызвавшего пользователя
- в этом случае возможно использование PHP для создания клиентских GUI-приложений
- для решения административных задач в операционных системах UNIX, Linux, Microsoft Windows, Mac OS X и AmigaOS.
- с версии PHP 5.4.0 в CLI SAPI появилась возможность запуска PHP как отдельного HTTP-сервера (один процесс интерпретатора и выполняет все запросы исключительно последовательно)

