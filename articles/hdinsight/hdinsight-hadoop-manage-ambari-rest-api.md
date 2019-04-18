---
title: Мониторинг и администрирование Hadoop с помощью REST API Ambari в Azure HDInsight
description: Сведения об использовании Ambari для отслеживания и администрирования кластеров Hadoop в Azure HDInsight. В этом документе рассказывается об использовании интерфейса REST API Ambari, предоставляемого с кластерами HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: hrasheed
ms.openlocfilehash: 37d4c4dbbe623d68edf1de5454d7752a81fc2bd2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58801889"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Управление кластерами HDInsight с помощью Apache Ambari REST API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Узнайте, как использовать Apache Ambari REST API для отслеживания и администрирования кластеров Apache Hadoop в Azure HDInsight.

## <a id="whatis"></a>Что такое Apache Ambari
[Apache Ambari](https://ambari.apache.org) упрощает управление и мониторинг кластеров Hadoop, предоставляя простой в использовании веб-Интерфейс его [интерфейсов REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari предоставляется по умолчанию с кластерами HDInsight на платформе Linux.

## <a name="prerequisites"></a>Технические условия
* **Кластер Hadoop в HDInsight**. См. в разделе [начало работы с HDInsight на платформе Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Bash на Ubuntu в Windows 10**.  В примерах в этой статье используется оболочка Bash в Windows 10. См. в разделе [подсистемы Windows для руководство по установке Linux для Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) для действия по установке.  Другие [оболочек Unix](https://www.gnu.org/software/bash/) также будут работать.  Примеры с некоторыми небольшими изменениями можно работать на командную строку Windows.  Кроме того можно использовать Windows PowerShell.

* **jq**, обработчик командной строки JSON.  Дополнительные сведения см. на странице [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* **Windows PowerShell**.  Кроме того, можно использовать [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uri-for-ambari-rest-api"></a>Базовый универсальный код ресурса для REST API Ambari

 Базовый URI для REST API Ambari в HDInsight — `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, где `CLUSTERNAME` — это имя вашего кластера.  Имена кластеров в URI, **с учетом регистра**.  Хотя имя кластера в полном доменном имени в URI (CLUSTERNAME.azurehdinsight.net) указывается без учета регистра, другие вхождения этого URI учитывают регистр.

## <a name="authentication"></a>Authentication

Подключение к Ambari в HDInsight выполняется по протоколу HTTPS. Используйте имя учетной записи администратора (по умолчанию — **admin**) и пароль, указанные при создании кластера.

## <a name="examples"></a>Примеры

### <a name="setup-preserve-credentials"></a>Программа установки (сохранять учетные данные)
Сохранить свои учетные данные, чтобы избежать повторного ввода их для каждого примера.  Имя кластера будет сохранен в рамках отдельного шага.

**A. Bash**  
Измените приведенный ниже скрипт, заменив `PASSWORD` на ваш фактический пароль.  Введите команду.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Определить имя правильно верблюжьим кластера
Фактический регистр имени кластера может отличаться от предполагается, что в зависимости от того, как был создан кластер.  При выполнении этих шагов будет Показать фактический регистр и затем сохранить ее в переменной для всех последующих примерах.

Изменение скриптов ниже, чтобы заменить `CLUSTERNAME` именем кластера. Введите команду. (Имя кластера для полного доменного ИМЕНИ выполняется без учета регистра.)

**A. Bash**  

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>Анализ данных JSON

В следующем примере используется [jq](https://stedolan.github.io/jq/) или [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) для синтаксического анализа документа ответа JSON и отображения только `health_report` сведения из результатов.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```   

### <a name="example-get-the-fqdn-of-cluster-nodes"></a> Получите полное ДОМЕННОЕ имя кластера узлов

При работе с HDInsight вам может потребоваться полное доменное имя (FQDN) узла кластера. FQDN можно легко получить для разных узлов в кластере с помощью следующих команд.

**Все узлы**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Головные узлы**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Рабочие узлы**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Узлы Zookeeper**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a> Получить внутренний IP-адрес из узлов кластера

IP-адреса, возвращенные в примерах из этого раздела, недоступны напрямую через Интернет. Они доступны только в пределах виртуальной сети Azure, содержащей кластер HDInsight.

Дополнительные сведения о работе с виртуальными сетями и HDInsight см. в статье [Расширение возможностей HDInsight с помощью виртуальной сети Azure](hdinsight-extend-hadoop-virtual-network.md).

Чтобы найти IP-адрес, необходимо знать внутренние полные доменные имена (FQDN) узлов кластера. Если у вас есть полное доменное имя, вы можете получить IP-адрес узла. В следующих примерах у Ambari сначала запрашивается полное доменное имя для всех узлов, а затем — IP-адрес каждого узла.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Получение хранилища по умолчанию

При создании кластера HDInsight в качестве хранилища по умолчанию для кластера необходимо использовать учетную запись хранения Azure или Data Lake Storage. Для получения этой информации после создания кластера можно использовать Ambari. Например, если нужно выполнить операции чтения или записи в контейнере за пределами HDInsight.

Следующие примеры извлекают конфигурацию хранилища, используемого в кластере по умолчанию.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Эти примеры возвращают первую конфигурацию, применяемую к серверу (`service_config_version=1`), который содержит эти сведения. Чтобы получить значение, которое было изменено после создания кластера, может потребоваться перечислить версии конфигурации и получить последнюю из них.

Возвращаемое значение будет выглядеть, как один из следующих примеров:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` — это значение указывает, что кластер использует учетную запись хранения Azure в качестве хранилища по умолчанию. Значение `ACCOUNTNAME` — имя учетной записи хранилища. Часть `CONTAINER` — это имя контейнера больших двоичных объектов в учетной записи хранения. Контейнер является корнем совместимого хранилища HDFS для кластера.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` — это значение указывает, что кластер использует Azure Data Lake Storage 2-го поколения в качестве хранилища по умолчанию. Значения `ACCOUNTNAME` и `CONTAINER` те же, что и для службы хранилища Azure, как объяснялось выше.

* `adl://home` — это значение указывает, что кластер использует Azure Data Lake Storage 1-го поколения в качестве хранилища по умолчанию.

    Чтобы найти имя учетной записи Data Lake Storage, используйте следующие примеры.

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Возвращается похожее значение: `ACCOUNTNAME.azuredatalakestore.net`, где `ACCOUNTNAME` — имя учетной записи Data Lake Storage.

    Чтобы найти каталог в Data Lake Storage, содержащий хранилище кластера, используйте следующие примеры.

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Вернется похожее значение: `/clusters/CLUSTERNAME/`. Это значение представляет путь в учетной записи Data Lake Storage. Путь является корнем совместимой файловой системы HDFS для кластера.  

> [!NOTE]  
> [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) командлет, предоставляемые [Azure PowerShell](/powershell/azure/overview) также возвращает сведения о хранилище для кластера.


### <a name="get-all-configurations"></a> Получить все конфигурации

Получите конфигурации, которые доступны для кластера.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Этот пример возвращает документ JSON с текущей конфигурацией (идентифицируется по значению *tag*) для компонентов, установленных в кластере. Ниже приведен пример фрагмента данных, возвращаемых из типа кластера Spark.
   
```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Получить конфигурацию для определенного компонента

Извлеките конфигурацию для интересующего вас компонента. В следующем примере замените `INITIAL` значением тега, возвращенным из предыдущего запроса.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Этот пример возвращает документ JSON, содержащий текущую конфигурацию для компонента `livy2-conf`.

### <a name="update-configuration"></a>Обновление конфигурации

1. Создайте `newconfig.json`.  
   Изменения, а затем введите следующие команды:

   * Замените `livy2-conf` с нужного компонента.
   * Замените `INITIAL` с фактическим значением, полученные для `tag` из [получить все конфигурации](#get-all-configurations).

     **A. Bash**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json

     ```

     **Б. powershell**  
     Сценарий PowerShell использует [jq](https://stedolan.github.io/jq/).  Изменить `C:\HD\jq\jq-win64` ниже, чтобы они соответствовали реальным путем и версию [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     jq используется для преобразования данных, извлеченных из HDInsight в новый шаблон конфигурации. В частности, эти примеры выполняют следующие действия.

   * Создает уникальное значение, содержащее строку version и дату, хранящуюся в `newtag`.

   * Создает корневой документ для новой требуемой конфигурации.

   * Возвращает содержимое массива `.items[]` и добавляет его в элемент **desired_config**.

   * Удаляет элементы `href`, `version` и `Config`, так как они не нужны для отправки новой конфигурации.

   * Добавляет элемент `tag` со значением `version#################`. Числовая часть основана на текущей дате. Каждая конфигурация должна иметь уникальное значение tag.

     Наконец, данные сохраняются в документе `newconfig.json`. Структура документа должна выглядеть следующим образом:

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Изменить `newconfig.json`.  
   Откройте документ `newconfig.json` и измените или добавьте значения в объекте `properties`. Следующий пример изменяет значение `"livy.server.csrf_protection.enabled"` с `"true"` на `"false"`.

        "livy.server.csrf_protection.enabled": "false",

    После завершения работы сохраните файл.

3. Отправить `newconfig.json`.  
   Выполните следующие команды, чтобы отправить обновленную конфигурацию в Ambari:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Эти команды передают содержимое файла **newconfig.json** в кластер в качестве новой требуемой конфигурации. Запрос возвращает документ JSON. Элемент **versionTag** в этом документе должен совпадать с отправленной версией, а объект **configs** содержит запрошенные изменения конфигурации.

### <a name="restart-a-service-component"></a>Перезапуск компонента службы

На этом этапе веб-интерфейс Ambari указывает на то, что вам необходимо перезапустить службу Spark, чтобы новая конфигурация вступила в силу. Для перезапуска службы выполните приведенные ниже действия.

1. Используйте следующие параметры для включения режима обслуживания для службы Spark2.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```

2. Проверка режима обслуживания  

    Эти команды отправляют документ JSON на сервер, который включает режим обслуживания. Проверить, находится ли служба в этом режиме, можно с помощью следующего запроса.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    Возвращается значение `ON`.

3. Затем выполните следующие для выключения службы Spark2.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://girouxSpark.azurehdinsight.net/api/v1/clusters/girouxspark/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    Ответ будет выглядеть примерно так:

    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```

    > [!IMPORTANT]  
    > Значение `href` , возвращенное этим универсальным кодом ресурса (URI), использует внутренний IP-адрес узла кластера. Чтобы использовать его извне кластера, замените часть "10.0.0.18:8080" на полное доменное имя кластера.  

4. Проверьте запрос.  
    Измените указанную ниже команду, заменив `29` с фактическим значением для `id` возвращается из предыдущего шага.  Например, следующие команды получают состояние запроса:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Ответ `COMPLETED` указывает, что запрос завершен.

5. После завершения предыдущего запроса, используйте следующее для запуска службы Spark2.
   
    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```

    Теперь служба использует новую конфигурацию.

6. Наконец, используйте следующую команду для отключения режима обслуживания.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'

    ```

## <a name="next-steps"></a>Дальнейшие действия

Полный справочник по REST API см. [здесь](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

