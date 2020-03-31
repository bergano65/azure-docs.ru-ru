---
title: Apache HBase Master не может начаться в Azure HDInsight
description: Apache HBase Master (HMaster) не может начаться в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887212"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) не может начаться в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Сценарий: Сбой атомного переименования

### <a name="issue"></a>Проблема

Неожиданные файлы, идентифицированные в процессе запуска.

### <a name="cause"></a>Причина

В процессе запуска HMaster выполняет множество этапов инициализации, включая перемещение папки данных с нуля (.tmp) в папку данных. HMaster также просматривает папку с записью (WAL), чтобы увидеть, есть ли какие-либо серверы региона без ответа.

HMaster выполняет базовую команду списка на папках WAL. Каждый раз, когда в любой из этих папок обнаруживается непредвиденный файл, возникает исключение и сервер не будет запущен.

### <a name="resolution"></a>Решение

Проверьте стек вызовов и попытайтесь определить, какая папка может вызвать проблему (например, это может быть папка WAL или папка .tmp). Затем в Cloud Explorer или с помощью команд HDFS попытайтесь найти файл с ошибкой. Как правило, `*-renamePending.json` это файл. (Файл `*-renamePending.json` — это файл журнала, используемый для реализации операции атомного переименования в драйвере WASB. Из-за ошибок в этой реализации эти файлы могут быть оставлены после сбоев процесса и так далее.) Принудительно удалите этот файл либо в Cloud Explorer, либо с помощью команд HDFS.

Иногда в этом месте также может `$$$.$$$` быть временный файл, названный что-то вроде. Чтобы увидеть этот файл, необходимо использовать команду `ls` HDFS. Этот файл не отображается в Cloud Explorer. Чтобы удалить этот файл, используйте команду HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.

После выполнения этих команд HMaster должен сразу запуститься.

---

## <a name="scenario-no-server-address-listed"></a>Сценарий: Адрес сервера не указан

### <a name="issue"></a>Проблема

Вы можете увидеть сообщение, `hbase: meta` указывававокоторое, что таблица не находится в сети. Запуск `hbck` может `hbase: meta table replicaId 0 is not found on any region.` сообщить, что в журналах HMaster `No server address listed in hbase: meta for region hbase: backup <region name>`можно увидеть сообщение: .  

### <a name="cause"></a>Причина

HMaster не смог инициализировать после перезапуска HBase.

### <a name="resolution"></a>Решение

1. В оболочке HBase введите следующие команды (при необходимости измените на фактические значения):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Удалите `hbase: namespace` запись. Эта запись может быть той же ошибкой, что сообщается при сканировании `hbase: namespace` таблицы.

1. Перезапустите активный сервер HMaster с помощью интерфейса Ambari, чтобы HBase стала работать.

1. В оболочке HBase выполните следующую команду, чтобы подключиться ко всем автономным таблицам:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Сценарий: java.io.IO Исключение: Приурочено

### <a name="issue"></a>Проблема

HMaster раз с фатальным `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`исключением похож на: .

### <a name="cause"></a>Причина

Такая проблема может возникнуть при наличии большого количества таблиц и регионов, которые не были удалены при перезагрузке служб HMaster. Тайм-аут является известным дефектом с HMaster. Общие задачи запуска кластера могут занять много времени. HMaster выключается, если таблица пространства имен еще не назначена. Длительные задачи запуска происходят там, где существует большое количество нефлешированных данных, и тайм-аутва в пять минут не является достаточным.

### <a name="resolution"></a>Решение

1. От Apache Ambari UI, перейдите на **HBase** > **Configs**. В пользовательском `hbase-site.xml` файле добавьте следующую настройку:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Перезапустите необходимые службы (HMaster или другие службы HBase).

---

## <a name="scenario-frequent-region-server-restarts"></a>Сценарий: Перезагрузка частого сервера региона

### <a name="issue"></a>Проблема

Узлы периодически перезагружают. Из журналов серверов региона можно увидеть записи, похожие на:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Причина

Длинная `regionserver` пауза JVM GC. Пауза приведет `regionserver` к безответным и не в состоянии отправить сердцебиение HMaster в течение 40-х годов сеанса zk. HMaster поверит, `regionserver` что мертв и `regionserver` прервет и перезапустит.

### <a name="resolution"></a>Решение

Изменить тайм-аут сеанса зоозащитника, `hbase-site` не только настройка, `zookeeper.session.timeout` но и настройки `zoo.cfg` `maxSessionTimeout` зоозащитника должны быть изменены.

1. Доступ Ambari UI, перейдите на **HBase-> Configs -> Настройки**, в разделе Таймаутс, изменить значение зоопарка сессии тайм-аут.

1. Доступ Ambari пользовательский доступ, перейдите на **зоозащитник -> Configs -> пользовательские** `zoo.cfg`, добавить / изменить следующие настройки. Убедитесь, что значение такое `zookeeper.session.timeout`же, как HBase .

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Перезагрузка необходимых услуг.

---

## <a name="scenario-log-splitting-failure"></a>Сценарий: Сбой разделения журнала

### <a name="issue"></a>Проблема

HMasters не удалось прийти на кластерhH.

### <a name="cause"></a>Причина

Неправильно настроенные настройки HDFS и HBase для вторичной учетной записи хранения.

### <a name="resolution"></a>Решение

set hbase.rootdir: wasb://@.blob.core.windows.net/hbase и перезапустите сервисы на Ambari.

---

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
