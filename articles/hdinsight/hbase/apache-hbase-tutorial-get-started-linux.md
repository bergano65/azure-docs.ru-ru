---
title: Учебник. Использование Apache HBase в Azure HDInsight
description: Ознакомьтесь с учебником об Apache HBase, чтобы начать использовать Hadoop в HDInsight. Создание таблиц из оболочки HBase и обращение к ним с помощью Hive.
keywords: hbasecommand, пример hbase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: hrasheed
ms.openlocfilehash: 48b02a042b55af9ff65f57220f7a64c9cbde8848
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445547"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Руководство по использованию Apache HBase в Azure HDInsight

В этом учебнике описано, как создавать кластеры Apache HBase в HDInsight, создавать таблицы HBase и запрашивать таблицы с помощью Apache Hive.  Общие сведения об HBase см. в статье [What is Apache HBase in Azure HDInsight](./apache-hbase-overview.md) (Что такое Apache HBase в HDInsight).

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание кластера Apache HBase
> * Создание таблиц HBase и вставка данных
> * Использование Apache Hive для создания запросов к Apache HBase
> * Использование API REST для HBase
> * Проверка состояния кластера

## <a name="prerequisites"></a>Предварительные требования

* Клиент SSH. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. В примерах, приведенных в этой статье, для команд curl используется оболочка Bash в Windows 10. Шаги установки см. в статье [Windows Subsystem for Linux Installation Guide for Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) (Подсистема Windows для Linux в Windows 10).  Другие [оболочки Unix](https://www.gnu.org/software/bash/) также будут работать.  Примеры curl с некоторыми небольшими изменениями могут работать в командной строке Windows.  Кроме того, вы можете использовать командлет Windows PowerShell [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Создание кластера Apache HBase

В следующей процедуре используется шаблон Azure Resource Manager, чтобы создать кластер HBase и зависимую учетную запись хранения Azure по умолчанию. Описание параметров, используемых в процедуре, и других методов создания кластеров см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Выберите следующее изображение, чтобы открыть шаблон на портале Azure. Шаблон находится на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. В колонке **Настраиваемое развертывание** укажите следующие значения.

    |Свойство |ОПИСАНИЕ |
    |---|---|
    |Подписка|Выберите подписку Azure, которая используется для создания этого кластера.|
    |Группа ресурсов|Создайте группу управления ресурсами Azure или выберите существующую.|
    |Расположение|Укажите расположение группы ресурсов. |
    |ClusterName|Укажите имя кластера HBase.|
    |Имя для входа и пароль для кластера|Имя для входа по умолчанию — **admin**.|
    |Имя пользователя SSH и пароль|Имя пользователя по умолчанию — **sshuser**.|

    Все остальные параметры являются необязательными.  

    У каждого кластера есть зависимость учетной записи хранения для службы хранилища Azure. После удаления кластера данные сохраняются в учетной записи хранения. Имя учетной записи хранения в кластере — это имя кластера, к которому добавлено слово store. Это прописано в разделе переменных в коде шаблона.

3. Установите флажок **Я принимаю указанные выше условия** и выберите **Приобрести**. Процесс создания кластера занимает около 20 минут.

После удаления кластера HBase можно создать другой кластер HBase, использовав тот же контейнер BLOB-объектов по умолчанию. Новый кластер получит таблицы HBase, созданные в исходном кластере. Перед удалением кластера рекомендуется отключить таблицы HBase, чтобы избежать несогласованности.

## <a name="create-tables-and-insert-data"></a>Создание таблиц и вставка данных

Для подключения к кластерам HBase можно использовать протокол SSH, а для создания таблиц HBase, вставки данных и создания запросов к данным — [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html).

Для большинства пользователей данные отображаются в табличном формате:

![Табличные данные HDInsight HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

В HBase (реализация [Cloud BigTable](https://cloud.google.com/bigtable/)) те же данные выглядят следующим образом:

![Данные BigTable HDInsight HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Использование оболочки HBase**

1. С помощью команды `ssh` подключитесь к кластеру HBase. Измените приведенную ниже команду, заменив `CLUSTERNAME` именем своего кластера, а затем введите команду:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. С помощью команды `hbase shell` запустите интерактивную оболочку HBase. В строку SSH-подключения введите следующую команду:

    ```bash
    hbase shell
    ```

1. С помощью команды `create` создайте таблицу HBase с двумя семействами столбцов. В именах таблиц и столбцов учитывается регистр. Введите следующую команду:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. С помощью команды `list` выведите список всех таблиц HBase. Введите следующую команду:

    ```hbase
    list
    ```

1. С помощью команды `put` вставьте значения в указанный столбец строки в определенной таблице. Введите следующие команды:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. С помощью команды `scan` выполните сканирование данных таблицы `Contacts` и верните их. Введите следующую команду:

    ```hbase
    scan 'Contacts'
    ```

    ![Оболочка HDInsight Hadoop HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. С помощью команды `get` получите содержимое строки. Введите следующую команду:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Вы увидите те же результаты, что и при использовании команды `scan`, так как в таблице есть только одна строка.

    Дополнительные сведения о схеме таблицы HBase см. в [этой статье](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Дополнительные команды HBase см. в [справочнике по Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. С помощью команды `exit` остановите интерактивную оболочку HBase. Введите следующую команду:

    ```hbaseshell
    exit
    ```

**Для массовой загрузки данных в таблицу контактов HBase**

HBase включает несколько методов загрузки данных в таблицы.  Для получения дополнительных сведений обратитесь к разделу [Массовая загрузка](https://hbase.apache.org/book.html#arch.bulk.load).

Пример файла данных находится в общедоступном контейнере больших двоичных объектов `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  Файл данных содержит:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

При желании вы можете создать текстовый файл и отправить его в свою учетную запись хранения. Дополнительные сведения см. в статье [Upload data for Apache Hadoop jobs in HDInsight](../hdinsight-upload-data.md) (Отправка данных для заданий Apache Hadoop в HDInsight).

Эта процедура использует таблицу `Contacts` HBase, созданную в последней процедуре.

1. В открытом сеансе SSH-подключения выполните следующую команду, чтобы преобразовать файл данных в StoreFiles и сохранить по относительному пути, указанному в `Dimporttsv.bulk.output`.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Для передачи данных из `/example/data/storeDataFileOutput` в таблицу HBase выполните следующую команду:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Откройте оболочку HBase и выполните команду `scan` для получения списка содержимого таблицы.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Использование Apache Hive для создания запросов к Apache HBase

Вы можете запрашивать данные в таблицах HBase с помощью [Apache Hive](https://hive.apache.org/). В этом разделе будет создана таблица Hive, которая сопоставляется с таблицей HBase и использует ее для формирования запросов к данным в таблице HBase.

1. В открытом сеансе SSH-подключения используйте следующую команду, чтобы запустить Beeline.

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Дополнительные сведения о Beeline см. в статье [Использование Hive с Hadoop в HDInsight с применением Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Выполните приведенный ниже скрипт [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual), чтобы создать таблицу Hive, сопоставляемую с таблицей HBase. Перед выполнением этого оператора убедитесь, что вы создали упомянутый в этой статье пример таблицы с помощью оболочки HBase.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Запустите приведенный ниже скрипт HiveQL, чтобы запросить данные в таблице HBase.

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Чтобы выйти из Beeline, используйте инструкцию `!exit`.

1. Чтобы выйти из сеанса SSH-подключения, используйте `exit`.

## <a name="use-hbase-rest-apis-using-curl"></a>Использование API REST для HBase

REST API защищен с помощью [обычной проверки подлинности](https://en.wikipedia.org/wiki/Basic_access_authentication). Чтобы обеспечить безопасную отправку учетных данных на сервер, все запросы следует отправлять с помощью протокола HTTPS.

1. Запустите переменную среды для простоты использования. Измените команды ниже, заменив `MYPASSWORD` паролем для входа в кластер. Замените `MYCLUSTERNAME` именем кластера HBase. Затем введите указанные ниже команды.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Для получения списка имеющихся таблиц HBase используйте следующую команду:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Для создания новой таблицы HBase с двумя семействами столбцов используйте следующую команду:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Схема предоставляется в формате JSON.
1. Чтобы вставить какие-либо данные, используйте следующую команду:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    Необходимо закодировать значения, указанные в параметре -d, используя кодировку base64. Ознакомьтесь со следующим примером:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) позволяет вставить несколько (пакетных) значений.

1. Для получения строки используйте следующую команду:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Дополнительные сведения см. в [справочнике по Apache HBase](https://hbase.apache.org/book.html#_rest).

> [!NOTE]  
> Thrift не поддерживается HBase в HDInsight.
>
> При использовании Curl или любых других средств связи REST с WebHCat нужно выполнять аутентификацию запросов с помощью пароля и имени пользователя администратора кластера HDInsight. Имя кластера необходимо также использовать в составе универсального кода ресурса (URI), используемого для отправки запросов на сервер.
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    Вы должны получить ответ, аналогичный приведенному ниже.
>   
>        {"status":"ok","version":"v1"}

## <a name="check-cluster-status"></a>Проверка состояния кластера

HBase на HDInsight поставляется с веб-интерфейсом для наблюдения за кластерами. С помощью веб-интерфейса вы можете запросить статистику или сведения о регионах.

**Доступ к основному интерфейсу HBase**

1. Войдите в веб-интерфейс Ambari по адресу `https://CLUSTERNAME.azurehdinsight.net`, где `CLUSTERNAME` — это имя кластера HBase.

1. В меню слева выберите **HBase**.

1. В верхней части страницы выберите **Быстрые ссылки**, выберите ссылку на активный узел Zookeeper, а затем щелкните **HBase Master UI** (Основной интерфейс HBase).  Интерфейс откроется в новой вкладке браузера.

   ![Основной интерфейс HDInsight HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   Основной интерфейс HBase состоит из таких разделов:

   - региональные серверы;
   - главные узлы резервного копирования;
   - таблицы;
   - задачи;
   - атрибуты ПО.

## <a name="clean-up-resources"></a>Очистка ресурсов

Перед удалением кластера рекомендуется отключить таблицы HBase, чтобы избежать несогласованности. Вы можете использовать команду HBase `disable 'Contacts'`. Если вы не собираетесь использовать это приложение в дальнейшем, удалите созданный кластер HBase, сделав следующее:

1. Войдите на [портале Azure](https://portal.azure.com/).
1. В поле **Поиск** в верхней части страницы введите **HDInsight**.
1. Выберите **Кластеры HDInsight** в разделе **Службы**.
1. В списке кластеров HDInsight, который отобразится, щелкните **...** рядом с кластером, созданным при работе с этим руководством.
1. Нажмите кнопку **Delete**(Удалить). Щелкните **Да**.

## <a name="next-steps"></a>Дополнительная информация

Из этого учебника вы узнали, как создать кластер Apache HBase, а также как создавать таблицы и просматривать данные в них из оболочки HBase. Вы также узнали, как использовать Hive для запроса данных из таблиц HBase и как использовать интерфейсы REST API на C# для HBase для создания таблицы HBase и извлечения данных из нее. Дополнительные сведения см. на следующих ресурсах:

> [!div class="nextstepaction"]
> [Что такое HBase в HDInsight: база данных NoSQL, которая предоставляет возможности, схожие BigTable, для Hadoop](./apache-hbase-overview.md)