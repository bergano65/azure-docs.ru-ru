---
title: Устранение неполадок Apache Oozie в Azure HDInsight
description: Устранение некоторых ошибок Apache Oozie в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 18831832f82cdbc8cec69e368f006f7acd4836c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82205265"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Устранение неполадок Apache Oozie в Azure HDInsight

С помощью пользовательского интерфейса Apache Oozie можно просматривать журналы Oozie. Пользовательский интерфейс Oozie также содержит ссылки на журналы JobTracker для заданий MapReduce, запущенных рабочим процессом. Действия по решению проблемы должны подчиняться следующему шаблону:

1. Просмотрите информацию о задании в веб-интерфейсе Oozie.

2. Если для определенного действия возникла ошибка или сбой, выберите действие, чтобы проверить, содержит ли поле **сообщения об ошибке** дополнительные сведения об ошибке.

3. Если известен URL-адрес действия, воспользуйтесь им для просмотра дополнительной информации о действии (например, журналов JobTracker).

Ниже приведены конкретные ошибки, которые могут возникнуть в, и способы их устранения.

## <a name="ja009-cant-initialize-cluster"></a>JA009: не удается инициализировать кластер

### <a name="issue"></a>Проблемы

Состояние задания изменяется на **SUSPENDED** (Приостановлено). В подробной информации о задании `RunHiveScript` состояние отображается как **START_MANUAL**. При выборе действия отображается следующее сообщение об ошибке:

    JA009: Cannot initialize Cluster. Please check your configuration for map

### <a name="cause"></a>Причина:

Адреса хранилища BLOB-объектов Azure, используемые в файле **job.xml**, не содержат контейнер хранилища или имя учетной записи хранения. Адрес хранилища BLOB-объектов должен иметь формат `wasbs://containername@storageaccountname.blob.core.windows.net`.

### <a name="resolution"></a>Решение

Измените адреса хранилища BLOB-объектов, используемые в задании.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: Oozie не разрешено олицетворять &lt; пользователя&gt;

### <a name="issue"></a>Проблемы

Состояние задания изменяется на **SUSPENDED** (Приостановлено). В подробной информации о задании `RunHiveScript` состояние отображается как **START_MANUAL**. Если выбрать действие, отобразится следующее сообщение об ошибке:

    JA002: User: oozie is not allowed to impersonate <USER>

### <a name="cause"></a>Причина:

Текущие права доступа не позволяют Oozie работать от имени учетной записи указанного пользователя.

### <a name="resolution"></a>Решение

Oozie может олицетворять пользователей в **`users`** группе. Для просмотра групп, в которые входит данный пользователь, воспользуйтесь командой `groups USERNAME` . Если пользователь не является членом **`users`** группы, добавьте пользователя в группу с помощью следующей команды:

    sudo adduser USERNAME users

> [!NOTE]  
> Чтобы служба HDInsight поняла, что пользователь добавлен в группу, может потребоваться несколько минут.

---

## <a name="launcher-error-sqoop"></a>ОШИБКА запуска (Sqoop)

### <a name="issue"></a>Проблемы

Состояние задания изменяется на **KILLED** (Прекращено). В подробной информации о задании `RunSqoopExport` состояние отображается как **ERROR**. Если выбрать действие, отобразится следующее сообщение об ошибке:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

### <a name="cause"></a>Причина:

Sqoop не удалось загрузить драйвер базы данных, необходимый для доступа к базе данных.

### <a name="resolution"></a>Решение

При использовании Sqoop из задания Oozie необходимо включить драйвер базы данных в ресурсы, используемые заданием, такие как workflow.xml. Кроме того, укажите архив, содержащий драйвер базы данных, из раздела `<sqoop>...</sqoop>` файла workflow.xml.

Например, для примера задания, [использующего рабочие процессы Hadoop Oozie](hdinsight-use-oozie-linux-mac.md), необходимо выполнить следующие действия.

1. Скопируйте `mssql-jdbc-7.0.0.jre8.jar` файл в каталог **Каталог/Tutorials/useoozie** :

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Измените файл `workflow.xml`, чтобы добавить следующий код XML в новую строку выше `</sqoop>`:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подпишитесь на [@AzureSupport](https://twitter.com/azuresupport) — официальный канал Microsoft Azure для работы с клиентами. Вступайте в сообщество Azure для получения нужных ресурсов: ответов, поддержки и советов экспертов.

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка**. Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).
