---
title: Устранение неполадок прерывистых ошибок исходящих соединений в службе приложений Azure
description: Устранение проблем прерывистые ошибки соединения и связанные с ними проблемы с производительностью в службе приложений Azure
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367555"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Устранение неполадок прерывистых ошибок исходящих соединений в службе приложений Azure

Эта статья поможет выснять периодические ошибки соединения и связанные с ними проблемы с производительностью в [Службе приложения Azure.](https://docs.microsoft.com/azure/app-service/overview) Эта тема предоставит более подробную информацию о методологиях устранения неполадок для исчерпания портов сети исходных адресов (SNAT). Если вам требуется дополнительная помощь в какой-либо момент этой статьи, обратитесь к экспертам Azure на [форумах MSDN Azure и Stack Overflow.](https://azure.microsoft.com/support/forums/) Кроме того, подайте в файл инцидента с поддержкой Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**

## <a name="symptoms"></a>Симптомы

Приложения и функции, размещенные в службе azure App, могут проявлять один или несколько следующих симптомов:

* Медленное время отклика на всех или некоторых экземплярах в плане обслуживания.
* Прерывистые ошибки 5xx или **Bad Gateway**
* Сообщения об ошибках тайм-аута
* Не удалось подключиться к внешним конечным точкам (например, S'LDB, Service Fabric, другим службам приложений и т.д.)

## <a name="cause"></a>Причина

Основной причиной этих симптомов является то, что экземпляр приложения не в состоянии открыть новое соединение к внешней конечной точке, поскольку он достиг одного из следующих пределов:

* Соединения TCP: Существует ограничение на количество исходящих соединений, которые могут быть сделаны. Это связано с размером используемого работника.
* SNAT ports: Как говорится в [исходящих соединениях в Azure,](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)Azure использует перевод исходных сетевых адресов (SNAT) и балансер нагрузки (не подвергающийся воздействию клиентов) для связи с конечными точками за пределами Azure в публичном пространстве IP-адресов. Каждому экземпляру в службе Azure App первоначально дается заранее выделенное число из **128** портов SNAT. Это ограничение влияет на открытие соединений с одной и той же комбинацией хоста и порта. Если приложение создает соединения с комбинациями адресов и портов, вы не будете использовать свои порты SNAT. Порты SNAT используются при повторных звонках по тому же адресу и комбинации портов. После того, как порт был освобожден, порт доступен для повторного использования по мере необходимости. Балансизатор нагрузки Сети Azure возвращает порт SNAT от закрытых соединений только после ожидания в течение 4 минут.

Когда приложения или функции быстро открывают новое соединение, они могут быстро исчерпать свою заранее выделенную квоту из 128 портов. Затем они блокируются до тех пор, пока не станет доступен новый порт SNAT, либо путем динамического распределения дополнительных портов SNAT, либо путем повторного использования рекультивированного порта SNAT. Приложения или функции, заблокированные из-за невозможности создания новых подключений, начнут испытывать одну или несколько проблем, описанных в разделе **Symptoms** этой статьи.

## <a name="avoiding-the-problem"></a>Как избежать проблемы

Избежание проблемы порта SNAT означает избежать создания новых соединений, повторяющихся для одного и того же узла и порта.

Общие стратегии смягчения исчерпания портов SNAT обсуждаются в [разделе решения проблем](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving) в **исходящих соединениях документации Azure.** Из этих стратегий следующие применимы к приложениям и функциям, размещенным в службе Azure App.

### <a name="modify-the-application-to-use-connection-pooling"></a>Изменение приложения для использования пулов подключений

* Для объединения соединений HTTP просмотрите [соединения пула HTTP с httpClientFactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Для получения информации об объединении соединения сервера S'L, просмотрите [пулинг подключения сервера S'L (ADO.NET).](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
* Для реализации объединения с приложениями инфраструктуры сущностей просмотрите [объединение DbContext.](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)

Ниже приведена коллекция ссылок для реализации объединения соединения в другой стек решений.

#### <a name="node"></a>Узел

По умолчанию соединения для Узлов не сохраняются. Ниже приведены популярные базы данных и пакеты для объединения соединений, которые содержат примеры того, как их реализовать.

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP Keep-alive

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Документация node.js v13.9.0 Документация](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Ниже приведены популярные библиотеки, используемые для объединения соединений JDBC, которые содержат примеры того, как их реализовать: JDBC Connection Pooling.

* [Томктат 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [ХикариКП](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

Http Подключение Пулинг

* [Управление подключением Apache](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [Класс PoolingHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Хотя PHP не поддерживает объединение соединений, можно попробовать использовать постоянные соединения базы данных на сервере.
 
* Сервер MyS'L

   * [Подключения MyS'Li](https://www.php.net/manual/mysqli.quickstart.connections.php) для новых версий
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) для старых версий PHP

* Другие источники данных

   * [Управление подключением PHP](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [Сервер S'L](https://github.com/tediousjs/node-mssql#connection-pools) (ПРИМЕЧАНИЕ: S'LAlchemy может быть использован с другими базами данных, кроме Сервера MicrosoftS'L)
* [HTTP Keep-alive](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)(Keep-Alive происходит автоматически при использовании [сеансов-объектов).](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)

Для других сред, обзор поставщика или драйвера конкретных документов для реализации объединения соединения в приложениях.

### <a name="modify-the-application-to-reuse-connections"></a>Изменение приложения для повторного использования подключений

*  Для дополнительных указателей и примеров управления соединениями в функциях Azure просмотрите [соединения Управления в Функциях Azure.](https://docs.microsoft.com/azure/azure-functions/manage-connections)

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Изменение приложения для использования менее "жесткой" логики повторных попыток

* Для получения дополнительных рекомендаций и примеров просмотрите [шаблон Retry.](https://docs.microsoft.com/azure/architecture/patterns/retry)

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Использование проверки активности для сброса времени ожидания простоя исходящих подключений

* Для реализации keepalives для приложений Node.js, просмотрите [приложение My node делает чрезмерные исходящие вызовы.](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls)

### <a name="additional-guidance-specific-to-app-service"></a>Дополнительные рекомендации, специфичные для Службы приложений:

* [Тест нагрузки](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test) должен имитировать данные реального мира в стабильной скорости кормления. Тестирование приложений и функций в условиях реального стресса может определить и решить проблемы истощения порта SNAT заблаговременно.
* Убедитесь, что службы бэк-энда могут быстро возвращать ответы. Для устранения неполадок с помощью базы данных Azure S'L просмотрите [проблемы с производительностью базы данных Troubleshoot Azure s'L с помощью Intelligent Insights.](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow)
* Масштабируйте план Службы приложений на большее время экземпляров. Дополнительные сведения о масштабировании см. в статье [Увеличение масштаба приложения в Azure](https://docs.microsoft.com/azure/app-service/manage-scale-up). Каждому экземпляру работника в плане обслуживания приложений выделяется ряд портов SNAT. Если вы распространяете использование в нескольких экземплярах, вы можете получить использование порта SNAT в экземпляре ниже рекомендуемого предела в 100 исходящих соединений на уникальную удаленную конечную точку.
* Рассмотрите возможность перехода на [среду службы приложений (ASE),](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)где вам выделяется один исходящий IP-адрес, а лимиты для подключений и портов SNAT намного выше.

Избежать исходящих ограничений TCP легче решить, так как ограничения устанавливаются размером вашего работника. Вы можете увидеть пределы в [Sandbox Cross VM Численные пределы - TCP соединения](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Ограничение имени|Описание|Малый (A1)|Средний (A2)|Большой (A3)|Изолированный ярус (ASE)|
|---|---|---|---|---|---|
|Соединения|Количество подключений по всему VM|1920|3968|8064|16 000|

Чтобы избежать исходящих ограничений TCP, можно либо увеличить размер ваших работников, либо масштабировать сяполивую горизонтально.

## <a name="troubleshooting"></a>Устранение неполадок

Зная два типа исходящих ограничений соединения, и то, что делает ваше приложение, должно облегчить устранение неполадок. Если вы знаете, что ваше приложение делает много звонков в одну и ту же учетную запись хранения, вы можете заподозрить ограничение SNAT. Если ваше приложение создает очень много звонков в конечные точки по всему Интернету, вы будете подозревать, что вы достигаете предела VM.

Если вы не знаете поведение приложения достаточно, чтобы определить причину быстро, Есть некоторые инструменты и методы, доступные в Службе App, чтобы помочь с этим определением.

### <a name="find-snat-port-allocation-information"></a>Найти информацию о распределении портов SNAT

Вы можете использовать [диагностику службы приложений](https://docs.microsoft.com/azure/app-service/overview-diagnostics) для поиска информации о распределении портов SNAT и наблюдайте за метрикой распределения портов SNAT на сайте службы приложений. Чтобы найти информацию о распределении портов SNAT, выполните следующие действия:

1. Чтобы получить доступ к диагностике Службы Приложений, перейдите в веб-приложение Службы App или среду службы приложений на [портале Azure.](https://portal.azure.com/) В левой навигации выберите **Диагностику и решить проблемы.**
2. Выберите категорию доступности и производительности
3. Выберите плитку SNAT Port Exhaustion в списке доступных плиток по категории. Практика заключается в том, чтобы держать его ниже 128.
Если вам это нужно, вы все еще можете открыть билет поддержки и инженер службы поддержки получит метрику от бэк-энда для вас.

Обратите внимание, что, поскольку использование порта SNAT недоступно в качестве метрики, невозможно ни автоматики, основанной на использовании порта SNAT, ни настроить автомасштаб, основанный на метрике распределения портов SNAT.

### <a name="tcp-connections-and-snat-ports"></a>TCP соединения и SNAT Порты

Соединения TCP и порты SNAT напрямую не связаны между собой. Детектор использования подключений TCP включен в лезвие диагностики и решения проблем любого сайта службы приложений. Поиск фразы "TCP соединения", чтобы найти его.

* Порты SNAT используются только для внешних сетевых потоков, в то время как общее подключение TCP включает локальные соединения loopback.
* Порт SNAT может быть общим для различных потоков, если потоки отличаются в протоколе, IP-адресе или порту. Метрика TCP Connections учитывает каждое соединение TCP.
* Лимит подключений TCP происходит на уровне экземпляра работника. Балансировка исходящих нагрузок сети Azure не использует метрику TCP Connections для ограничения порта SNAT.
* Ограничения подключения TCP описаны в [Sandbox Cross VM Numerical Limits - TCP Connections](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Ограничение имени|Описание|Малый (A1)|Средний (A2)|Большой (A3)|Изолированный ярус (ASE)|
|---|---|---|---|---|---|
|Соединения|Количество подключений по всему VM|1920|3968|8064|16 000|

### <a name="webjobs-and-database-connections"></a>Соединения WebJobs и базы данных
 
Если порты SNAT исчерпаны, где WebJobs не может подключиться к базе данных Azure S'L, нет метрики, чтобы показать, сколько соединений открывается каждым отдельным процессом веб-приложений. Чтобы найти проблемную WebJob, переместите несколько WebJobs в другой план службы приложений, чтобы узнать, улучшается ли ситуация, или если проблема остается в одном из планов. Повторите этот процесс до тех пор, пока не найдете проблемный WebJob.

### <a name="using-snat-ports-sooner"></a>Использование портов SNAT раньше

Вы не можете изменить настройки Azure, чтобы выпустить используемые порты SNAT раньше, так как все порты SNAT будут выпущены в рамках нижеприведенных условий, а поведение будет разработано.
 
* Если сервер или клиент отправляет FINACK, [порт SNAT будет выпущен](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release) через 240 секунд.
* Если RST виден, порт SNAT будет выпущен через 15 секунд.
* Если тайм-аут был достигнут, порт освобождается.
 
## <a name="additional-information"></a>Дополнительные сведения

* [SNAT с app Service](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Устранение причин низкой производительности приложения в Службе приложений Azure](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
