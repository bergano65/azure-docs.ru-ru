---
title: Справочник по экземплярам контейнеров Azure YAML
description: Справочник по файлу YAML, поддерживаемому экземплярами контейнеров Azure для настройки группы контейнеров
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/12/2019
ms.author: danlep
ms.openlocfilehash: 2e6be18371cf3ff96d1ce91d4dde26ff1f14021b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179908"
---
# <a name="yaml-reference-azure-container-instances"></a>Справочник по YAML: Экземпляры контейнеров Azure

В этой статье описывается синтаксис и свойства файла YAML, поддерживаемого экземплярами контейнеров Azure, для настройки [группы контейнеров](container-instances-container-groups.md). Используйте файл YAML для ввода конфигурации группы в команду [AZ Container Create][az-container-create] в Azure CLI. 

Файл YAML — это удобный способ настройки группы контейнеров для воспроизводимых развертываний. Это краткая альтернатива использованию [шаблона диспетчер ресурсов](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) или пакетов SDK для экземпляров контейнеров Azure для создания или обновления группы контейнеров.

> [!NOTE]
> Эта ссылка относится к файлам YAML для экземпляров контейнеров Azure REST API версии `2018-10-01`.

## <a name="schema"></a>Схема 

Ниже приведена схема для файла YAML, включая комментарии для выделения ключевых свойств. Описание свойств этой схемы см. в разделе [значения свойств](#property-values) .

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # Exposed ports on the instance
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>Значения свойств

В следующих таблицах описаны значения, которые следует указать в этой схеме.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Объект Microsoft. Контаинеринстанце/Контаинерграупс

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Имя группы контейнеров. |
|  apiVersion | enum | Да | 2018-10-01 |
|  расположение | строка | Нет | Расположение ресурса. |
|  теги | object | Нет | Теги ресурсов. |
|  identity | object | Нет | Удостоверение группы контейнеров, если оно настроено. - [Объект Контаинерграупидентити](#ContainerGroupIdentity) |
|  свойства | object | Да | [Объект Контаинерграуппропертиес](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Объект Контаинерграупидентити

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  type | enum | Нет | Тип удостоверения, используемого для группы контейнеров. Тип "SystemAssigned, Усерассигнед" включает как неявно созданное удостоверение, так и набор удостоверений, назначенных пользователем. Тип "нет" удалит все удостоверения из группы контейнеров. -SystemAssigned, Усерассигнед, SystemAssigned, Усерассигнед, нет |
|  userAssignedIdentities | object | Нет | Список удостоверений пользователей, связанных с группой контейнеров. Ссылки на ключ словаря удостоверений пользователя будут Azure Resource Manager идентификаторы ресурсов в формате: '/Субскриптионс/{субскриптионид}/ресаурцеграупс/{ресаурцеграупнаме}/провидерс/Микрософт.манажедидентити/усерассигнедидентитиес/{идентитинаме }'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Объект Контаинерграуппропертиес

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  containers | array | Да | Контейнеры в группе контейнеров. - [Объект контейнера](#Container) |
|  imageRegistryCredentials | array | Нет | Учетные данные реестра образов, по которым создается группа контейнеров. - [Объект Имажерегистрикредентиал](#ImageRegistryCredential) |
|  restartPolicy | enum | Нет | Перезапустите политику для всех контейнеров в группе контейнеров. - `Always`Всегда перезагружать `OnFailure` -перезапустить при `Never` сбое — никогда не перезапускать. Всегда, onFailure, никогда |
|  IP | object | Нет | Тип IP-адреса группы контейнеров. - [IpAddress, объект](#IpAddress) |
|  osType | enum | Да | Тип операционной системы, необходимый контейнерам в группе контейнеров. — Windows или Linux |
|  тома | array | Нет | Список томов, которые могут быть подключены контейнерами в этой группе контейнеров. - [Объект Volume](#Volume) |
|  диагностика | object | Нет | Диагностические сведения для группы контейнеров. - [Объект Контаинерграупдиагностикс](#ContainerGroupDiagnostics) |
|  NetworkProfile | object | Нет | Сведения о сетевом профиле для группы контейнеров. - [Объект Контаинерграупнетворкпрофиле](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | Нет | Сведения о конфигурации DNS для группы контейнеров. - [Объект Днсконфигуратион](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Объект контейнера

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Предоставленное пользователем имя экземпляра контейнера. |
|  свойства | object | Да | Свойства экземпляра контейнера. - [Объект Контаинерпропертиес](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Объект Имажерегистрикредентиал

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  server | строка | Да | Сервер реестра образа DOCKER без протокола, например "http" и "HTTPS". |
|  username | строка | Да | Имя пользователя для частного реестра. |
|  password | строка | Нет | Пароль для частного реестра. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress, объект

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  ports; | array | Да | Список портов, предоставляемых в группе контейнеров. - [Объект Port](#Port) |
|  type | enum | Да | Указывает, является ли IP-адрес общедоступным или частной ВИРТУАЛЬНОЙ сетью. — Открытый или частный |
|  ip | строка | Нет | IP-адрес, предоставляемый общедоступному Интернету. |
|  днснамелабел | строка | Нет | Метка имени DNS для IP-адреса. |


<a id="Volume" />

### <a name="volume-object"></a>Объект Volume

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Имя тома. |
|  азурефиле | object | Нет | Том файлов Azure. - [Объект Азурефилеволуме](#AzureFileVolume) |
|  emptyDir | object | Нет | Пустой том каталога. |
|  secret | object | Нет | Секретный том. |
|  gitRepo | object | Нет | Том репозитория Git. - [Объект Гитреповолуме](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Объект Контаинерграупдиагностикс

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Нет | Сведения о log Analytics для группы контейнеров. - [Объект LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Объект Контаинерграупнетворкпрофиле

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  id | строка | Да | Идентификатор сетевого профиля. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Объект Днсконфигуратион

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  Серверах имен | array | Да | DNS-серверы для группы контейнеров. -String |
|  сеарчдомаинс | строка | Нет | Домены поиска DNS для поиска имени узла в группе контейнеров. |
|  параметры | строка | Нет | Параметры DNS для группы контейнеров. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Объект Контаинерпропертиес

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  image | строка | Да | Имя образа, используемого для создания экземпляра контейнера. |
|  command | array | Нет | Команды для выполнения в экземпляре контейнера в Exec Form. -String |
|  ports; | array | Нет | Доступные порты в экземпляре контейнера. - [Объект Контаинерпорт](#ContainerPort) |
|  environmentVariables | array | Нет | Переменные среды, которые необходимо задать в экземпляре контейнера. - [Объект EnvironmentVariable](#EnvironmentVariable) |
|  ресурсы | object | Да | Требования к ресурсам для экземпляра контейнера. - [Объект Ресаурцерекуирементс](#ResourceRequirements) |
|  волумемаунтс | array | Нет | Тома, доступные для экземпляра контейнера. - [Объект Волумемаунт](#VolumeMount) |
|  ливенесспробе | object | Нет | Проверка актуальности. - [Объект Контаинерпробе](#ContainerProbe) |
|  реадинесспробе | object | Нет | Проба готовности. - [Объект Контаинерпробе](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Объект Port

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | Нет | Протокол, связанный с портом. -TCP или UDP |
|  port | integer | Да | Номер порта. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Объект Азурефилеволуме

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  Сетев | строка | Да | Имя файлового ресурса Azure, который будет подключен как том. |
|  readOnly | boolean | Нет | Флаг, указывающий, является ли общий файл Azure, подключенный как том, только для чтения. |
|  storageAccountName | строка | Да | Имя учетной записи хранения, содержащей файловый ресурс Azure. |
|  storageAccountKey | строка | Нет | Ключ доступа к учетной записи хранения, используемый для доступа к файловому ресурсу Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Объект Гитреповолуме

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  каталог | строка | Нет | Имя целевого каталога. Не должен содержать или начинаться с "..".  Если указан параметр ".", каталог томов будет репозиторием Git.  В противном случае, если он указан, том будет содержать репозиторий Git в подкаталоге с заданным именем. |
|  репозиторий | строка | Да | URL-адрес репозитория |
|  версия | строка | Нет | Зафиксировать хэш для указанной редакции. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Объект LogAnalytics

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  workspaceId | строка | Да | Идентификатор рабочей области для log Analytics |
|  workspaceKey | строка | Да | Ключ рабочей области для log Analytics |
|  логтипе | enum | Нет | Используемый тип журнала. -Контаинеринсигхтс или Контаинеринстанцелогс |
|  метаданные | object | Нет | Метаданные для log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Объект Контаинерпорт

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | Нет | Протокол, связанный с портом. -TCP или UDP |
|  port | integer | Да | Номер порта, предоставленный в группе контейнеров. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Объект EnvironmentVariable

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Имя переменной среды. |
|  value | строка | Нет | Значение переменной среды. |
|  secureValue | строка | Нет | Значение переменной защищенной среды. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Объект Ресаурцерекуирементс

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  запросы | object | Да | Запросы ресурса этого экземпляра контейнера. - [Объект Ресаурцерекуестс](#ResourceRequests) |
|  ограничения | object | Нет | Ограничения ресурсов этого экземпляра контейнера. - [Объект Ресаурцелимитс](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Объект Волумемаунт

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Имя подключения тома. |
|  mountPath | строка | Да | Путь в контейнере, куда должен быть подключен том. Не должно содержать двоеточие (:). |
|  readOnly | boolean | Нет | Флаг, указывающий, что подключение тома доступно только для чтения. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Объект Контаинерпробе

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  Exec | object | Нет | Команда выполнения для проверки [объекта контаинерексек](#ContainerExec) |
|  httpGet | object | Нет | Объект HTTP Get Settings to зонд- [контаинерхттпжет](#ContainerHttpGet) |
|  инитиалделайсекондс | integer | Нет | Начальная задержка в секундах. |
|  периодсекондс | integer | Нет | Период в секундах. |
|  фаилуресрешолд | integer | Нет | Пороговое значение сбоя. |
|  сукцесссрешолд | integer | Нет | Пороговое значение успеха. |
|  timeoutSeconds | integer | Нет | Время ожидания (в секундах). |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Объект Ресаурцерекуестс

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  меморингб | number | Да | Запрос памяти в ГБ этого экземпляра контейнера. |
|  цп | number | Да | Запрос ЦП этого экземпляра контейнера. |
|  Графический процессор | object | Нет | Запрос GPU этого экземпляра контейнера. - [Объект Гпуресаурце](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Объект Ресаурцелимитс

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  меморингб | number | Нет | Предельный объем памяти (в ГБ) данного экземпляра контейнера. |
|  цп | number | Нет | Ограничение ЦП данного экземпляра контейнера. |
|  Графический процессор | object | Нет | Предельный размер графического процессора для этого экземпляра контейнера. - [Объект Гпуресаурце](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Объект Контаинерексек

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  command | array | Нет | Команды для выполнения в контейнере. -String |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Объект Контаинерхттпжет

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  path | строка | Нет | Путь для проверки. |
|  port | integer | Да | Номер порта для проверки. |
|  схема | enum | Нет | Схема. -HTTP или HTTPS |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Объект Гпуресаурце

|  Название | Type | Обязательное значение | Значение |
|  ---- | ---- | ---- | ---- |
|  количество | integer | Да | Число ресурсов GPU. |
|  sku | enum | Да | Номер SKU ресурса GPU. -K80, P100, V100 |


## <a name="next-steps"></a>Следующие шаги

См. Руководство по [развертыванию группы с несколькими контейнерами с помощью файла YAML](container-instances-multi-container-yaml.md).

См. примеры использования файла YAML для развертывания групп контейнеров в [виртуальной сети](container-instances-vnet.md) или [подключения внешнего тома](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

