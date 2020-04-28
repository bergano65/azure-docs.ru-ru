---
title: Справочник по YAML для группы контейнеров
description: Справочник по файлу YAML, поддерживаемому экземплярами контейнеров Azure для настройки группы контейнеров
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74896572"
---
# <a name="yaml-reference-azure-container-instances"></a>Справочник по YAML: экземпляры контейнеров Azure

В этой статье описывается синтаксис и свойства файла YAML, поддерживаемого экземплярами контейнеров Azure, для настройки [группы контейнеров](container-instances-container-groups.md). Используйте файл YAML для ввода конфигурации группы в команду [AZ Container Create][az-container-create] в Azure CLI. 

Файл YAML — это удобный способ настройки группы контейнеров для воспроизводимых развертываний. Это краткая альтернатива использованию [шаблона диспетчер ресурсов](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) или пакетов SDK для экземпляров контейнеров Azure для создания или обновления группы контейнеров.

> [!NOTE]
> Эта ссылка относится к файлам YAML для экземпляров контейнеров Azure REST API версии `2018-10-01`.

## <a name="schema"></a>схема 

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
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
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

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Имя группы контейнеров. |
|  версия_API | enum | Да | 2018-10-01 |
|  location | строка | Нет | Местоположение ресурса. |
|  tags | object | Нет | Теги ресурсов. |
|  identity | object | Нет | Удостоверение группы контейнеров, если оно настроено. - [Объект Контаинерграупидентити](#ContainerGroupIdentity) |
|  properties | object | Да | [Объект Контаинерграуппропертиес](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Объект Контаинерграупидентити

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  type | enum | Нет | Тип удостоверения, используемого для группы контейнеров. Тип "SystemAssigned, Усерассигнед" включает как неявно созданное удостоверение, так и набор удостоверений, назначенных пользователем. Тип "нет" удалит все удостоверения из группы контейнеров. -SystemAssigned, Усерассигнед, SystemAssigned, Усерассигнед, нет |
|  userAssignedIdentities | object | Нет | Список удостоверений пользователей, связанных с группой контейнеров. Ссылки на ключ словаря удостоверений пользователя будут Azure Resource Manager идентификаторы ресурсов в формате "/Субскриптионс/{субскриптионид}/ресаурцеграупс/{ресаурцеграупнаме}/провидерс/Микрософт.манажедидентити/усерассигнедидентитиес/{идентитинаме}". |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Объект Контаинерграуппропертиес

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  containers | массиве | Да | Контейнеры в группе контейнеров. - [Объект контейнера](#Container) |
|  imageRegistryCredentials | массиве | Нет | Учетные данные реестра образов, по которым создается группа контейнеров. - [Объект Имажерегистрикредентиал](#ImageRegistryCredential) |
|  restartPolicy | enum | Нет | Перезапустите политику для всех контейнеров в группе контейнеров. - `Always`Всегда перезагружать `OnFailure` -перезапустить при `Never` сбое — никогда не перезапускать. Всегда, onFailure, никогда |
|  ipAddress | object | Нет | Тип IP-адреса группы контейнеров. - [IpAddress, объект](#IpAddress) |
|  osType | enum | Да | Тип операционной системы, необходимый контейнерам в группе контейнеров. — Windows или Linux |
|  volumes. | массиве | Нет | Список томов, которые могут быть подключены контейнерами в этой группе контейнеров. - [Объект Volume](#Volume) |
|  диагностика | object | Нет | Диагностические сведения для группы контейнеров. - [Объект Контаинерграупдиагностикс](#ContainerGroupDiagnostics) |
|  networkProfile | object | Нет | Сведения о сетевом профиле для группы контейнеров. - [Объект Контаинерграупнетворкпрофиле](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | Нет | Сведения о конфигурации DNS для группы контейнеров. - [Объект Днсконфигуратион](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Объект контейнера

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Предоставленное пользователем имя экземпляра контейнера. |
|  properties | object | Да | Свойства экземпляра контейнера. - [Объект Контаинерпропертиес](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Объект Имажерегистрикредентиал

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  server | строка | Да | Сервер реестра образа DOCKER без протокола, например "http" и "HTTPS". |
|  username | строка | Да | Имя пользователя для частного реестра. |
|  пароль | строка | Нет | Пароль для частного реестра. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress, объект

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  ports; | массиве | Да | Список портов, предоставляемых в группе контейнеров. - [Объект Port](#Port) |
|  type | enum | Да | Указывает, является ли IP-адрес общедоступным или частной ВИРТУАЛЬНОЙ сетью. — Открытый или частный |
|  см | строка | Нет | IP-адрес, предоставляемый общедоступному Интернету. |
|  днснамелабел | строка | Нет | Метка имени DNS для IP-адреса. |


<a id="Volume" />

### <a name="volume-object"></a>Объект Volume

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Имя тома. |
|  азурефиле | object | Нет | Том файлов Azure. - [Объект Азурефилеволуме](#AzureFileVolume) |
|  emptyDir | object | Нет | Пустой том каталога. |
|  secret | object | Нет | Секретный том. |
|  gitRepo | object | Нет | Том репозитория Git. - [Объект Гитреповолуме](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Объект Контаинерграупдиагностикс

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | Нет | Сведения о log Analytics для группы контейнеров. - [Объект LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Объект Контаинерграупнетворкпрофиле

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  идентификатор | строка | Да | Идентификатор сетевого профиля. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Объект Днсконфигуратион

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  Серверах имен | массиве | Да | DNS-серверы для группы контейнеров. -String |
|  сеарчдомаинс | строка | Нет | Домены поиска DNS для поиска имени узла в группе контейнеров. |
|  параметры | строка | Нет | Параметры DNS для группы контейнеров. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Объект Контаинерпропертиес

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  Изображение | строка | Да | Имя образа, используемого для создания экземпляра контейнера. |
|  command | массиве | Нет | Команды для выполнения в экземпляре контейнера в Exec Form. -String |
|  ports; | массиве | Нет | Доступные порты в экземпляре контейнера. - [Объект Контаинерпорт](#ContainerPort) |
|  environmentVariables | массиве | Нет | Переменные среды, которые необходимо задать в экземпляре контейнера. - [Объект EnvironmentVariable](#EnvironmentVariable) |
|  ресурсов | object | Да | Требования к ресурсам для экземпляра контейнера. - [Объект Ресаурцерекуирементс](#ResourceRequirements) |
|  волумемаунтс | массиве | Нет | Тома, доступные для экземпляра контейнера. - [Объект Волумемаунт](#VolumeMount) |
|  ливенесспробе | object | Нет | Проверка актуальности. - [Объект Контаинерпробе](#ContainerProbe) |
|  реадинесспробе | object | Нет | Проба готовности. - [Объект Контаинерпробе](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Объект Port

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | Нет | Протокол, связанный с портом. -TCP или UDP |
|  порт | Целое число | Да | номер порта. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Объект Азурефилеволуме

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  Сетев | строка | Да | Имя файлового ресурса Azure, который будет подключен как том. |
|  readOnly | Логическое | Нет | Флаг, указывающий, является ли общий файл Azure, подключенный как том, только для чтения. |
|  storageAccountName | строка | Да | Имя учетной записи хранения, содержащей файловый ресурс Azure. |
|  storageAccountKey | строка | Нет | Ключ доступа к учетной записи хранения, используемый для доступа к файловому ресурсу Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Объект Гитреповолуме

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  directory. | строка | Нет | Имя целевого каталога. Не должен содержать или начинаться с "..".  Если указан параметр ".", каталог томов будет репозиторием Git.  В противном случае, если он указан, том будет содержать репозиторий Git в подкаталоге с заданным именем. |
|  repository | строка | Да | URL-адрес репозитория |
|  версия | строка | Нет | Зафиксировать хэш для указанной редакции. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Объект LogAnalytics

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  workspaceId | строка | Да | Идентификатор рабочей области для log Analytics |
|  workspaceKey | строка | Да | Ключ рабочей области для log Analytics |
|  логтипе | enum | Нет | Используемый тип журнала. -Контаинеринсигхтс или Контаинеринстанцелогс |
|  метаданные | object | Нет | Метаданные для log Analytics. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Объект Контаинерпорт

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | Нет | Протокол, связанный с портом. -TCP или UDP |
|  порт | Целое число | Да | Номер порта, предоставленный в группе контейнеров. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Объект EnvironmentVariable

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Имя переменной среды. |
|  value | строка | Нет | Значение переменной среды. |
|  secureValue | строка | Нет | Значение переменной защищенной среды. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Объект Ресаурцерекуирементс

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  requests | object | Да | Запросы ресурса этого экземпляра контейнера. - [Объект Ресаурцерекуестс](#ResourceRequests) |
|  ограничения | object | Нет | Ограничения ресурсов этого экземпляра контейнера. - [Объект Ресаурцелимитс](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Объект Волумемаунт

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Имя подключения тома. |
|  mountPath | строка | Да | Путь в контейнере, куда должен быть подключен том. Не должно содержать двоеточие (:). |
|  readOnly | Логическое | Нет | Флаг, указывающий, что подключение тома доступно только для чтения. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Объект Контаинерпробе

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  exec | object | Нет | Команда выполнения для проверки [объекта контаинерексек](#ContainerExec) |
|  httpGet | object | Нет | Объект HTTP Get Settings to зонд- [контаинерхттпжет](#ContainerHttpGet) |
|  инитиалделайсекондс | Целое число | Нет | Начальная задержка в секундах. |
|  периодсекондс | Целое число | Нет | Период в секундах. |
|  фаилуресрешолд | Целое число | Нет | Пороговое значение сбоя. |
|  сукцесссрешолд | Целое число | Нет | Пороговое значение успеха. |
|  timeoutSeconds | Целое число | Нет | Время ожидания (в секундах). |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Объект Ресаурцерекуестс

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  меморингб | number | Да | Запрос памяти в ГБ этого экземпляра контейнера. |
|  cpu | number | Да | Запрос ЦП этого экземпляра контейнера. |
|  совмещен | object | Нет | Запрос GPU этого экземпляра контейнера. - [Объект Гпуресаурце](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Объект Ресаурцелимитс

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  меморингб | number | Нет | Предельный объем памяти (в ГБ) данного экземпляра контейнера. |
|  cpu | number | Нет | Ограничение ЦП данного экземпляра контейнера. |
|  совмещен | object | Нет | Предельный размер графического процессора для этого экземпляра контейнера. - [Объект Гпуресаурце](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Объект Контаинерексек

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  command | массиве | Нет | Команды для выполнения в контейнере. -String |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Объект Контаинерхттпжет

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  path | строка | Нет | Путь для проверки. |
|  порт | Целое число | Да | Номер порта для проверки. |
|  scheme | enum | Нет | Схема. -HTTP или HTTPS |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Объект Гпуресаурце

|  Имя | Type | Обязательный | Значение |
|  ---- | ---- | ---- | ---- |
|  count | Целое число | Да | Число ресурсов GPU. |
|  sku | enum | Да | Номер SKU ресурса GPU. -K80, P100, V100 |


## <a name="next-steps"></a>Дальнейшие действия

См. Руководство по [развертыванию группы с несколькими контейнерами с помощью файла YAML](container-instances-multi-container-yaml.md).

См. примеры использования файла YAML для развертывания групп контейнеров в [виртуальной сети](container-instances-vnet.md) или [подключения внешнего тома](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

