---
title: Мониторинг и администрирование Hadoop с помощью REST API Ambari в Azure HDInsight
description: Сведения об использовании Ambari для отслеживания и администрирования кластеров Hadoop в Azure HDInsight. В этом документе вы узнаете, как использовать API Ambari REST, включенный в кластеры HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 317d12f6d5dee92d998266d4e9b6d52e6ef9c7a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381377"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Управление кластерами HDInsight с помощью Apache Ambari REST API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Узнайте, как использовать Apache Ambari REST API для отслеживания и администрирования кластеров Apache Hadoop в Azure HDInsight.

## <a name="what-is-apache-ambari"></a>Что такое Апач Амбари

[Apache Ambari](https://ambari.apache.org) упрощает управление и мониторинг кластеров Hadoop, предоставляя простой в использовании веб-ui при поддержке [его REST AIS.](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)  Ambari предоставляется по умолчанию с кластерами HDInsight на платформе Linux.

## <a name="prerequisites"></a>Предварительные требования

* Кластер Хадуп на HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Bash на Ubuntu на Windows 10.  В примерах, приведенных в этой статье, используется оболочка Bash в Windows 10. Шаги установки см. в статье [Windows Subsystem for Linux Installation Guide for Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) (Подсистема Windows для Linux в Windows 10).  Другие [оболочки Unix](https://www.gnu.org/software/bash/) также будут работать.  Примеры, с некоторыми незначительными изменениями, могут работать на запросе команды Windows.  Или вы можете использовать Windows PowerShell.

* jq — обработчик командной строки JSON.  Смотрите [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Windows PowerShell  Или вы можете использовать [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Базовый единый идентификатор ресурсов для API отдыха Ambari

 Базовый единый идентификатор ресурсов (URI) для Ambari REST API на HDInsight — это `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`место, где `CLUSTERNAME` находится название вашего кластера.  Названия кластеров в URIs **чувствительны к случаям.**  В то время как название кластера в полностью квалифицированном`CLUSTERNAME.azurehdinsight.net`доменном имени (ФЗДН) части URI () является нечувствительным к случаям, другие случаи в URI чувствительны к случаям.

## <a name="authentication"></a>Аутентификация

Подключение к Ambari в HDInsight выполняется по протоколу HTTPS. Используйте имя учетной записи администратора (по умолчанию — **admin**) и пароль, указанные при создании кластера.

Для кластеров пакетов корпоративной `admin`безопасности вместо того, чтобы использовать полностью квалифицированное имя пользователя, например. `username@domain.onmicrosoft.com`

## <a name="examples"></a>Примеры

### <a name="setup-preserve-credentials"></a>Настройка (Сохранение учетных данных)

Сохраните свои учетные данные, чтобы избежать повторного ввода их для каждого примера.  Название кластера будет сохранено в отдельном шаге.

**А. Баш**  
Отоверьте сценарий ниже, заменив `PASSWORD` фактический пароль.  Затем введите команду.

```bash
export password='PASSWORD'
```  

**B. ПауэрШелл**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Определение правильного кейсированного названия кластера

Фактический корпус имени кластера может отличаться от ожидаемого.  Шаги здесь будут отображать фактический корпус, а затем хранить его в переменной для всех более поздних примеров.

Отспособьте приведенные ниже `CLUSTERNAME` сценарии, чтобы заменить имя кластера. Затем введите команду. (Название кластера для ФЗДН не является чувствительным к случаям.)

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

В следующем примере используется [jq](https://stedolan.github.io/jq/) или [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) для разбора `health_report` документа ответа JSON и отображения только информации из результатов.

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

### <a name="get-the-fqdn-of-cluster-nodes"></a>Получение полного доменного имени для узлов кластера

Возможно, вам потребуется знать полностью квалифицированное доменное имя (ФЗДН) кластерного узла. FQDN можно легко получить для разных узлов в кластере с помощью следующих команд.

**Все узлы**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
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
| jq -r '.host_components[].HostRoles.host_name'
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
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Узлы зоозащитника**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>Получение внутреннего IP-адреса узлов кластера

IP-адреса, возвращенные примерами в этом разделе, не доступны напрямую через Интернет. Они доступны только в виртуальной сети Azure, которая содержит кластер HDInsight.

Для получения дополнительной информации о работе [Plan a virtual network for HDInsight](hdinsight-plan-virtual-network-deployment.md)с HDInsight и виртуальными сетями, см.

Чтобы найти IP-адрес, необходимо знать внутренние полные доменные имена (FQDN) узлов кластера. Если у вас есть полное доменное имя, вы можете получить IP-адрес узла. Следующие примеры первого запроса Ambari для F-DN всех узлов хоста. Затем запрашивает Ambari для IP-адреса каждого хоста.

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

Кластеры HDInsight должны использовать учетную запись хранения Azure или хранилище озер данных в качестве хранилища по умолчанию. Для получения этой информации после создания кластера можно использовать Ambari. Например, если нужно выполнить операции чтения или записи в контейнере за пределами HDInsight.

Следующие примеры извлекают конфигурацию хранилища, используемого в кластере по умолчанию.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
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

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` — это значение указывает, что кластер использует учетную запись хранения Azure в качестве хранилища по умолчанию. Значение `ACCOUNTNAME` — имя учетной записи хранилища. Часть `CONTAINER` — это имя контейнера больших двоичных объектов в учетной записи хранения. Контейнер является корнем совместимого хранилища HDFS для кластера.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` — это значение указывает, что кластер использует Azure Data Lake Storage 2-го поколения в качестве хранилища по умолчанию. Значения `ACCOUNTNAME` и `CONTAINER` те же, что и для службы хранилища Azure, как объяснялось выше.

* `adl://home` — это значение указывает, что кластер использует Azure Data Lake Storage 1-го поколения в качестве хранилища по умолчанию.

    Чтобы найти имя учетной записи Data Lake Storage, используйте следующие примеры.

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
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
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Вернется похожее значение: `/clusters/CLUSTERNAME/`. Это значение представляет путь в учетной записи Data Lake Storage. Путь является корнем совместимой файловой системы HDFS для кластера.  

> [!NOTE]  
> [Cmdlet Get-AzHDInsightCluster,](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) предоставленный [Azure PowerShell,](/powershell/azure/overview) также возвращает информацию о хранении для кластера.

### <a name="get-all-configurations"></a>Получить все конфигурации

Получите конфигурации, которые доступны для кластера.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Этот пример возвращает документ JSON, содержащий текущую конфигурацию для установленных компонентов. Просмотрите значение *тегов.* Ниже приведен пример фрагмента данных, возвращаемых из типа кластера Spark.

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

### <a name="get-configuration-for-specific-component"></a>Получить конфигурацию для конкретного компонента

Получите конфигурацию для интересуемого компонента. В следующем примере замените `INITIAL` значением тега, возвращенным из предыдущего запроса.

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
   Измените, а затем введите команды ниже:

   * Заменить `livy2-conf` новым компонентом.
   * Заменить `INITIAL` с фактическим `tag` значением, извлеченным из [Get всех конфигураций.](#get-all-configurations)

     **А. Баш**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. ПауэрШелл**  
     Скрипт PowerShell использует [jq](https://stedolan.github.io/jq/).  Отоверьте `C:\HD\jq\jq-win64` ниже, чтобы отразить ваш фактический путь и версию [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     jq используется для преобразования данных, извлеченных из HDInsight в новый шаблон конфигурации. В частности, в этих примерах приводятся следующие действия:

   * Создает уникальное значение, содержащее строку version и дату, хранящуюся в `newtag`.

   * Создает корневой документ для новой конфигурации.

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

2. Измените `newconfig.json`.  
   Откройте документ `newconfig.json` и измените или добавьте значения в объекте `properties`. Следующий пример изменяет значение `"livy.server.csrf_protection.enabled"` с `"true"` на `"false"`.

        "livy.server.csrf_protection.enabled": "false",

    Сохранить файл, как только вы сделали внесении изменений.

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

    Эти команды отправляют содержимое файла **newconfig.json** в кластер в качестве новой конфигурации. Запрос возвращает документ JSON. Элемент **versionTag** в этом документе должен совпадать с отправленной версией, а объект **configs** содержит запрошенные изменения конфигурации.

### <a name="restart-a-service-component"></a>Перезапуск компонента службы

На этом этапе веб-uI Ambari указывает на то, что служба Spark должна быть перезапущена до вхучить новую конфигурацию. Для перезапуска службы выполните приведенные ниже действия.

1. Используйте следующее, чтобы включить режим обслуживания для службы Spark2:

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
    ```

2. Проверить режим обслуживания  

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

3. Далее используйте следующее, чтобы отключить службу Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
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
    > Значение `href` , возвращенное этим универсальным кодом ресурса (URI), использует внутренний IP-адрес узла кластера. Чтобы использовать его из-за `10.0.0.18:8080` пределов кластера, замените часть f-DN кластера.  

4. Проверить запрос.  
    Отображай приведенную `29` ниже команду, `id` заменяя фактическое значение, возвращенное с предыдущего шага.  Например, следующие команды получают состояние запроса:

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

5. Как только предыдущий запрос будет завершен, используйте следующее, чтобы запустить службу Spark2.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
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

## <a name="next-steps"></a>Следующие шаги

Полный справочник по REST API см. [здесь](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Смотрите также, [авторизовать пользователей для Apache Ambari Просмотров](./hdinsight-authorize-users-to-ambari.md)
