---
title: Устранение неполадок Apache Oozie в Azure HDInsight
description: Устранение некоторых ошибок Apache Oozie в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: edbe5274de8576fccb29e1e69d260a6531d4ab05
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287407"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Устранение неполадок Apache Oozie в Azure HDInsight

С помощью пользовательского интерфейса Apache Oozie можно просматривать журналы Oozie. Пользовательский интерфейс Oozie также содержит ссылки на журналы JobTracker для заданий MapReduce, запущенных рабочим процессом. Действия по решению проблемы должны подчиняться следующему шаблону:

1. Просмотрите информацию о задании в веб-интерфейсе Oozie.

2. Если для определенного действия возникла ошибка или сбой, выберите действие, чтобы проверить, содержит ли поле **сообщения об ошибке** дополнительные сведения об ошибке.

3. Если известен URL-адрес действия, воспользуйтесь им для просмотра дополнительной информации о действии (например, журналов JobTracker).

Ниже приведены конкретные ошибки, которые могут возникнуть в, и способы их устранения.

## <a name="ja009-cant-initialize-cluster"></a>JA009: не удается инициализировать кластер

### <a name="issue"></a>Проблема

Состояние задания изменяется на **SUSPENDED** (Приостановлено). В подробной информации о задании `RunHiveScript` состояние отображается как **START_MANUAL**. При выборе действия отображается следующее сообщение об ошибке:

```output
JA009: Cannot initialize Cluster. Please check your configuration for map
```

### <a name="cause"></a>Причина

Адреса хранилища BLOB-объектов Azure, используемые в файле **job.xml** , не содержат контейнер хранилища или имя учетной записи хранения. Адрес хранилища BLOB-объектов должен иметь формат `wasbs://containername@storageaccountname.blob.core.windows.net`.

### <a name="resolution"></a>Решение

Измените адреса хранилища BLOB-объектов, используемые в задании.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: Oozie не разрешено олицетворять &lt; пользователя&gt;

### <a name="issue"></a>Проблема

Состояние задания изменяется на **SUSPENDED** (Приостановлено). В подробной информации о задании `RunHiveScript` состояние отображается как **START_MANUAL**. Если выбрать действие, отобразится следующее сообщение об ошибке:

```output
JA002: User: oozie is not allowed to impersonate <USER>
```

### <a name="cause"></a>Причина

Текущие права доступа не позволяют Oozie работать от имени учетной записи указанного пользователя.

### <a name="resolution"></a>Решение

Oozie может олицетворять пользователей в **`users`** группе. Для просмотра групп, в которые входит данный пользователь, воспользуйтесь командой `groups USERNAME` . Если пользователь не является членом **`users`** группы, добавьте пользователя в группу с помощью следующей команды:

```bash
sudo adduser USERNAME users
```

> [!NOTE]  
> Чтобы служба HDInsight поняла, что пользователь добавлен в группу, может потребоваться несколько минут.

---

## <a name="launcher-error-sqoop"></a>ОШИБКА запуска (Sqoop)

### <a name="issue"></a>Проблема

Состояние задания изменяется на **KILLED** (Прекращено). В подробной информации о задании `RunSqoopExport` состояние отображается как **ERROR**. Если выбрать действие, отобразится следующее сообщение об ошибке:

```output
Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]
```

### <a name="cause"></a>Причина

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

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]