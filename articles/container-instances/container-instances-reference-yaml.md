---
title: Ссылка YAML для контейнерной группы
description: Ссылка на файл YAML, поддерживаемый экземплярами контейнеров Azure для настройки группы контейнеров
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896572"
---
# <a name="yaml-reference-azure-container-instances"></a>Ссылка YAML: Экземпляры контейнеров Azure

В этой статье рассматриваются синтаксис и свойства файла YAML, поддерживаемый Azure Container Instances для настройки [группы контейнеров.](container-instances-container-groups.md) Используйте файл YAML для ввода групповой конфигурации в [контейнер az, создавая][az-container-create] команду в Azure CLI. 

Файл YAML — это удобный способ настройки контейнерной группы для воспроизводимых развертываний. Это краткая альтернатива использованию [шаблона «Менеджер ресурсов»](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) или SDK Azure Container Instances для создания или обновления группы контейнеров.

> [!NOTE]
> Эта ссылка применяется к файлам YAML для `2018-10-01`версии Rest API Azure Container Instances REST API.

## <a name="schema"></a>схема 

Схема для файла YAML, включая комментарии, чтобы выделить ключевые свойства. Для описания свойств в этой [схеме](#property-values) см.

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

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Объект Microsoft.ContainerInstance/containerGroups

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Название контейнерной группы. |
|  версия_API | enum | Да | 2018-10-01 |
|  location | строка | нет | Местоположение ресурса. |
|  tags | объект | нет | Теги ресурсов. |
|  identity | объект | нет | Идентификация группы контейнеров, если настроена. - [Объект ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | объект | Да | [Объект ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Объект ContainerGroupIdentity

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  type | enum | нет | Тип идентификации, используемый для группы контейнеров. Тип 'SystemAssigned, UserAssigned' включает в себя как неявно созданную идентификацию, так и набор назначенных пользователем идентификационных данных. Тип 'None' удалит любые идентификаторы из группы контейнеров. - СистемаНазначено, UserAssigned, SystemAssigned, UserAssigned, Нет |
|  userAssignedIdentities | объект | нет | Список идентификаторов пользователей, связанных с группой контейнеров. Ключевыми ссылками словаря идентификации пользователя будут идентификаторы ресурсов Ресурсного отдела Azure Resource Manager в форме: '/подписок/'подписка/"подписка/ресурсгруппы/ресурсGroup/'resourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/'identityName'". |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Объект ContainerGroupProperties

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  containers | массиве | Да | Контейнеры внутри контейнерной группы. - [Контейнерный объект](#Container) |
|  imageRegistryCredentials | массиве | нет | Учетные данные реестра изображений, из которых создается группа контейнеров. - [Объект ImageRegistryCredential](#ImageRegistryCredential) |
|  перезапускПолитика | enum | нет | Политика перезагрузки для всех контейнеров в группе контейнеров. - `Always`Всегда перезагрузка- `OnFailure` Перезагрузка `Never` на отказ- Никогда не перезагрузить. - Всегда, Onfailure, никогда |
|  ipAddress | объект | нет | Тип IP-адреса контейнерной группы. - [Объект IpAddress](#IpAddress) |
|  osType | enum | Да | Тип операционной системы, требуемый контейнерами в контейнерной группе. - Windows или Linux |
|  volumes. | массиве | нет | Список томов, которые могут быть установлены контейнерами в этой группе контейнеров. - [Объект объема](#Volume) |
|  диагностика | объект | нет | Диагностическая информация для контейнерной группы. - [Объект ContainerGroupДиагностики](#ContainerGroupDiagnostics) |
|  networkProfile | объект | нет | Информация профиля сети для контейнерной группы. - [Объект ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig | объект | нет | DNS конфискует информацию для контейнерной группы. - [Объект DnsConfiguration](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Контейнерный объект

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Пользовательское имя экземпляра контейнера. |
|  properties | объект | Да | Свойства экземпляра контейнера. - [Объект ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Объект ImageRegistryCredential

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  server | строка | Да | Сервер реестра изображений Docker без протокола, такого как "http" и "https". |
|  username | строка | Да | Имя пользователя для частного реестра. |
|  password | строка | нет | Пароль для частного реестра. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>Объект IpAddress

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  ports; | массиве | Да | Список портов, выставленных на контейнерную группу. - [Объект порта](#Port) |
|  type | enum | Да | Определяет, если IP подвергается воздействию государственного интернета или частного VNET. - Государственные или частные |
|  Ip | строка | нет | IP подвергается общественности Интернет. |
|  dnsNameLabel | строка | нет | Метка имени Dns для IP. |


<a id="Volume" />

### <a name="volume-object"></a>Объект объема

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Имя тома. |
|  лазурныйфайл | объект | нет | Объем файла Azure. - [Объект AzureFileVolume](#AzureFileVolume) |
|  emptyDir | объект | нет | Пустой объем каталога. |
|  secret | объект | нет | Секретный том. |
|  gitRepo | объект | нет | Объем git репо. - [Объект GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Объект ContainerGroupДиагностики

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | объект | нет | Информация о журнале журналов контейнерных групп. - [Объект LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Объект ContainerGroupNetworkProfile

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  идентификатор | строка | Да | Идентификатор сетевого профиля. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>Объект DnsConfiguration

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  имяСерверы | массиве | Да | DNS-серверы для контейнерной группы. - строка |
|  поисковые домены | строка | нет | Домены поиска DNS для поиска хостимен в группе контейнеров. |
|  параметры | строка | нет | Варианты DNS для контейнерной группы. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Объект ContainerProperties

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  Изображение | строка | Да | Имя изображения, используемого для создания экземпляра контейнера. |
|  command | массиве | нет | Команды для выполнения в экземпляре контейнера в форме exec. - строка |
|  ports; | массиве | нет | Открытые порты на экземпляре контейнера. - [Объект ContainerPort](#ContainerPort) |
|  environmentVariables | массиве | нет | Переменные среды, установленные в экземпляре контейнера. - [Окружающая средаПеременный объект](#EnvironmentVariable) |
|  ресурсов | объект | Да | Требования к ресурсам экземпляра контейнера. - [Объект ResourceRequirements](#ResourceRequirements) |
|  volumeMounts | массиве | нет | Объем крепления доступны для экземпляра контейнера. - [Объект VolumeMount](#VolumeMount) |
|  живостьЗонд | объект | нет | Зонд живости. - [Объект ContainerProbe](#ContainerProbe) |
|  readinessProbe | объект | нет | Зонд готовности. - [Объект ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Объект порта

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | нет | Протокол, связанный с портом. - TCP или UDP |
|  порт | Целое число | Да | номер порта. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Объект AzureFileVolume

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  shareName | строка | Да | Название доли файла Azure, которая должна быть установлена в виде тома. |
|  readOnly | Логическое | нет | Флаг, указывающий на то, является ли файл Azure общим, установленным в виде тома, только для чтения. |
|  storageAccountName | строка | Да | Имя учетной записи хранилища, содержащей долю файла Azure. |
|  storageAccountKey | строка | нет | Ключ доступа к учетной записи хранилища, используемый для доступа к кадоте файла Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Объект GitRepoVolume

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  directory. | строка | нет | Целевое имя каталога. Не должны содержать или начать с '..'.  Если '.' поставляется, каталог объемов будет репозиторием git.  В противном случае, если указано, том будет содержать репозиторий git в субдиректоре с указанным именем. |
|  repository | строка | Да | URL-адрес репозитория |
|  версия | строка | нет | Обязательство хэша для указанного пересмотра. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Объект LogAnalytics

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  workspaceId | строка | Да | Идентификатор рабочего пространства для анализа журналов |
|  workspaceKey | строка | Да | Ключ рабочей области для анализа журналов |
|  logType | enum | нет | Тип журнала, который будет использоваться. - ContainerInsights или ContainerInstanceLogs |
|  метаданные | объект | нет | Метаданные для анализа журналов. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Объект ContainerPort

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | нет | Протокол, связанный с портом. - TCP или UDP |
|  порт | Целое число | Да | Номер порта, выставленный внутри контейнерной группы. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Окружающая средаПеременный объект

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Имя переменной среды. |
|  значение | строка | нет | Значение переменной среды. |
|  secureValue | строка | нет | Значение переменной безопасной среды. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Объект ResourceRequirements

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  requests | объект | Да | Запросы ресурсов этого экземпляра контейнера. - [РесурсЗапросы объекта](#ResourceRequests) |
|  ограничения | объект | нет | Пределы ресурсов этого экземпляра контейнера. - [Объект ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Объект VolumeMount

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  name | строка | Да | Название монтажа тома. |
|  mountPath | строка | Да | Путь внутри контейнера, где объем должен быть установлен. Не должно содержать толстой кишки (:). |
|  readOnly | Логическое | нет | Флаг, указывающий, является ли монтировка громкости только прочитанном. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Объект ContainerProbe

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  exec | объект | нет | Команда выполнения для зондирования - [объект ContainerExec](#ContainerExec) |
|  httpGet | объект | нет | Http Получить настройки для зонда - [ContainerHttpGet объекта](#ContainerHttpGet) |
|  первоначальнаяЗадержкаSeconds | Целое число | нет | Первоначальная задержка секунд. |
|  периодСекунды | Целое число | нет | Период секунд. |
|  сбойThreshold | Целое число | нет | Порог сбоя. |
|  успехThreshold | Целое число | нет | Порог успеха. |
|  тайм-аутSeconds | Целое число | нет | Секунды тайм-аута. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>РесурсЗапросы объекта

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | Да | Запрос памяти в ГБ этого экземпляра контейнера. |
|  cpu | number | Да | Запрос процессора этого экземпляра контейнера. |
|  Графический процессор | объект | нет | Запрос GPU этого экземпляра контейнера. - [Объект GpuResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Объект ResourceLimits

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | нет | Ограничение памяти в ГБ этого экземпляра контейнера. |
|  cpu | number | нет | Предел процессора этого экземпляра контейнера. |
|  Графический процессор | объект | нет | Лимит GPU этого экземпляра контейнера. - [Объект GpuResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Объект ContainerExec

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  command | массиве | нет | Команды для выполнения в контейнере. - строка |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>Объект ContainerHttpGet

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  path | строка | нет | Путь к зонду. |
|  порт | Целое число | Да | Номер порта для зондирования. |
|  scheme | enum | нет | Схема. - http или https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Объект GpuResource

|  name | Тип | Обязательно | Значение |
|  ---- | ---- | ---- | ---- |
|  count | Целое число | Да | Подсчет ресурсов GPU. |
|  sku | enum | Да | SKU ресурса GPU. - K80, P100, V100 |


## <a name="next-steps"></a>Дальнейшие действия

Смотрите учебник [Развертывание группы из нескольких контейнеров с помощью файла YAML.](container-instances-multi-container-yaml.md)

Смотрите примеры использования файла YAML для развертывания контейнерных групп в [виртуальной сети](container-instances-vnet.md) или [установки внешнего объема.](container-instances-volume-azure-files.md)

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

