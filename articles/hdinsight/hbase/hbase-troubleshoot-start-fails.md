---
title: Не удается запустить Apache HBase Master в Azure HDInsight
description: Не удается запустить Apache HBase Master (HMaster) в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/06/2019
ms.openlocfilehash: 8368ebfca4cdd72c5c455a04e29b6c0cb44938ea
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935410"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Не удается запустить Apache HBase Master (HMaster) в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Сценарий: Сбой атомарной операции переименования

### <a name="issue"></a>Проблемы

В процессе запуска обнаружены непредвиденные файлы.

### <a name="cause"></a>Причина:

В процессе запуска HMaster выполняет множество шагов инициализации, включая перемещение данных из папки "Рабочая область" (. tmp) в папку данных. HMaster также рассматривает папку Wal (заменяя журналы), чтобы узнать, есть ли какие-либо серверы с неработающими регионами. Во всех этих ситуациях он выполняет простую `list` команду для этих папок. Если в любой момент обнаруживается непредвиденный файл в любой из этих папок, возникает исключение и, следовательно, не запускается.

### <a name="resolution"></a>Разрешение

В таком случае проверьте стек вызовов, чтобы узнать, какая папка может вызвать проблему (например, папка Wal или папка TMP). Затем с помощью Cloud Explorer или с помощью команд HDFS найдите проблемный файл. Файл проблемы обычно `*-renamePending.json` является файлом (файлом журнала, используемым для реализации атомарной операции переименования в драйвере WASB). Из-за ошибок в этой реализации такие файлы могут остаться в случае сбоя процесса. Принудительно удалить этот файл с помощью Cloud Explorer. Кроме того, в этом расположении может существовать временный файл с характером $. Файл невозможно просмотреть с помощью Cloud Explorer и только через команду HDFS `ls` . Для удаления этого файла можно `hdfs dfs -rm //\$\$\$.\$\$\$` использовать команду HDFS.

После удаления проблемного файла HMaster должен быть немедленно запущен.

---

## <a name="scenario-no-server-address-listed"></a>Сценарий: Адрес сервера отсутствует в списке

### <a name="issue"></a>Проблемы

В журнале HMaster отображается сообщение об ошибке, похожее на "нет адреса сервера, указанного в HBase: meta для региона XXX".

### <a name="cause"></a>Причина:

Не удалось инициализировать HMaster после перезапуска HBase.

### <a name="resolution"></a>Разрешение

1. Выполните следующие команды в оболочке HBase (Измените фактические значения, как применимо):

    ```
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>' 
    ```

1. Удалите запись пространства имен hbase, чтобы та же ошибка не возникла во время сканирования таблицы пространства имен hbase.

1. Перезапустите активный сервер HMaster с помощью интерфейса Ambari, чтобы HBase стала работать.

1. Выполните следующую команду в оболочке HBase, чтобы подключиться ко всем автономным таблицам.

    ```
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Сценарий: Java. IO. IOException: TimedOut

### <a name="issue"></a>Проблемы

Время ожидания HMaster истекает с неустранимым исключением, например `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`.

### <a name="cause"></a>Причина:

Время ожидания является известным дефектом HMaster. Общие задачи запуска кластера могут занять много времени. HMaster завершает работу, если таблица пространства имен еще не назначена. Длительные задачи запуска выполняются, когда существует большой объем неочищенных данных и не хватает времени ожидания 5 минут.

### <a name="resolution"></a>Разрешение

1. Доступ к пользовательскому интерфейсу Ambari, переход к конфигурации HBase-> `hbase-site.xml` в пользовательском виде добавьте следующий параметр:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Перезапустите необходимые службы (главным образом HMaster или другие службы HBase).

---

## <a name="scenario-frequent-regionserver-restarts"></a>Сценарий: Частые перезагрузки регионсервер

### <a name="issue"></a>Проблемы

Периодически перезапускаются узлы. В журналах регионсервер могут отображаться записи, аналогичные следующим:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Причина:

Длительная регионсервер ВИРТУАЛЬНОЙ машины Java сборка мусора. Приостановка приведет к тому, что регионсервер не будет отвечать на запросы и не сможет отправить HMaster пульса в течение времени ожидания ZK сеанса 40s. HMaster полагает, что регионсервер не существует и прекратит регионсервер и перезапустить.

### <a name="resolution"></a>Разрешение

Измените значение времени ожидания сеанса Zookeeper, а не только для параметра `zookeeper.session.timeout` HBase-site, но также Zookeeper `maxSessionTimeout` настройку Zoo. cfg необходимо изменить.

1. Доступ к пользовательскому интерфейсу Ambari, перейдите в раздел **HBase-> configs-> Settings**(время ожидания) и измените значение времени ожидания сеанса Zookeeper.

1. Доступ к пользовательскому интерфейсу Ambari, перейдите к **Zookeeper-> configs-> Custom** Zoo. cfg, добавьте или измените следующий параметр. Убедитесь, что значение совпадает с HBase `zookeeper.session.timeout`.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Перезапустите необходимые службы.

---

## <a name="scenario-log-splitting-failure"></a>Сценарий: Ошибка разбиения журнала

### <a name="issue"></a>Проблемы

Хмастерс не удалось подключиться к кластеру HBase.

### <a name="cause"></a>Причина:

Неправильно настроены параметры HDFS и HBase для дополнительной учетной записи хранения.

### <a name="resolution"></a>Разрешение

Установите HBase. рутдир: wasb://@.blob.core.windows.net/hbase и перезапустите службы на Ambari.

---

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключение с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
