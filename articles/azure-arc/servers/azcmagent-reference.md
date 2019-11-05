---
title: Интерфейс CLI агента подключенного компьютера Azure
description: Справочная документация по интерфейсу командной строки агента подключенного компьютера Azure
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513202"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Интерфейс CLI агента подключенного компьютера Azure

Средство `Azcmagent` (агент подключенных компьютеров Azure) используется для настройки и устранения неполадок при подключении к Azure на компьютерах, не относящихся к Azure.

Сам агент — это управляющий процесс, именуемый `himdsd` в Linux, и служба Windows, именуемая `himds` в Windows.

При нормальном использовании `azcmagent connect` используется для установления подключения между этим компьютером и Azure и `azcmagent disconnect`, если вы решили больше не использовать это подключение. Другие команды предназначены для устранения неполадок или в других особых случаях.

## <a name="options"></a>Параметры

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>СМ. ТАКЖЕ

* [азкмажент Connect](#azcmagent-connect) — подключает этот компьютер к Azure.
* [азкмажент Disconnect — отключение](#azcmagent-disconnect) компьютера от Azure
* [азкмажент Reconnect](#azcmagent-reconnect) — повторно подключает этот компьютер к Azure
* [азкмажент-отобразить](#azcmagent-show) — получение метаданных и состояния агента компьютера. Это в первую очередь полезно для устранения неполадок.
* [версия азкмажент](#azcmagent-version) — отображение версии агента гибридного управления

## <a name="azcmagent-connect"></a>азкмажент подключение

Подключает этот компьютер к Azure

### <a name="synopsis"></a>Краткий обзор

Создает ресурс в Azure, представляющий этот компьютер.

При этом используются параметры проверки подлинности, предоставленные для создания ресурса в Azure Resource Manager, представляющем этот компьютер. Ресурс находится в запрошенной подписке и группе ресурсов, а данные о компьютере хранятся в регионе Azure, указанном параметром location.
Имя ресурса по умолчанию является именем узла этого компьютера, если оно не переопределено.

Затем сертификат, соответствующий назначенному системой удостоверению этого компьютера, загружается и сохраняется локально. После завершения этого шага служба **метаданных компьютера, подключенного к Azure** , и агент гостевой конфигурации начинают синхронизацию с облаком Azure.

Параметры проверки подлинности:

* `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>` маркера доступа
* ИДЕНТИФИКАТОР и секрет субъекта-службы `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Вход на устройство (интерактивный) `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>Синтаксис

```none
azcmagent connect [flags]
```

### <a name="options"></a>Параметры

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>азкмажент отключение

Отключает этот компьютер от Azure

### <a name="synopsis"></a>Краткий обзор

Удаляет ресурс в Azure, который представляет этот сервер.

Эта команда использует параметры проверки подлинности, предоставленные для удаления ресурса Azure Resource Manager, представляющего этот компьютер. После этой точки **Служба метаданных компьютера, подключенного к Azure** , и агент гостевой конфигурации будут отключены. Эта команда не останавливает или не удаляет службы. для этого удалите пакет.

Эта команда требует более высоких привилегий, чем роль "подключение к подключенному компьютеру Azure".

После отключения компьютера используйте `azcmagent connect`, а не `azcmagent reconnect`, если вы хотите создать для нее новый ресурс в Azure.

Параметры проверки подлинности:

* `azcmagent disconnect --access-token <>` маркера доступа
* ИДЕНТИФИКАТОР и секрет субъекта-службы `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Интерактивное входное устройство `azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>Синтаксис

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>Параметры

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>азкмажент повторное подключение

Повторно подключает этот компьютер к Azure

### <a name="synopsis"></a>Краткий обзор

Повторно подключите компьютер с недействительными учетными данными в Azure.

Если у компьютера уже есть ресурс в Azure, но он не может пройти проверку подлинности, его можно подключить с помощью этой команды. Это возможно, если компьютер был отключен достаточно долго, чтобы срок его действия истек (не менее 45 дней).

Если компьютер был отключен с `azcmagent disconnect`, используйте вместо него `azcmagent connect`.

Эта команда использует параметры проверки подлинности, предоставленные для получения новых учетных данных, соответствующих ресурсу Azure Resource Manager, представляющему этот компьютер.

Эта команда требует более высоких привилегий, чем роль **подключения компьютера, подключенного к Azure** .

Параметры аутентификации

* `azcmagent reconnect --access-token <>` маркера доступа
* ИДЕНТИФИКАТОР и секрет субъекта-службы `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Интерактивное входное устройство `azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>Синтаксис

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>Параметры

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>азкмажент показывать

Возвращает метаданные компьютера и состояние агента. Это в первую очередь полезно для устранения неполадок.

### <a name="synopsis"></a>Краткий обзор

Возвращает метаданные компьютера и состояние агента. Это в первую очередь полезно для устранения неполадок.


### <a name="syntax"></a>Синтаксис

```
azcmagent show [flags]
```

### <a name="options"></a>Параметры

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>Версия азкмажент

Отображение версии агента гибридного управления

### <a name="synopsis"></a>Краткий обзор

Отображение версии агента гибридного управления

### <a name="syntax"></a>Синтаксис

```none
azcmagent version [flags]
```

### <a name="options"></a>Параметры

```none
  -h, --help   help for version
```
