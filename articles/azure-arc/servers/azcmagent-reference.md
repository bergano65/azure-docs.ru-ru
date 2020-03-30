---
title: Интерфейс CLI агента подключенного компьютера Azure
description: Справочная документация для агента Azure Connected Machine CLI
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513202"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Интерфейс CLI агента подключенного компьютера Azure

Инструмент `Azcmagent` (Azure Connected Machine Agent) используется для настройки и устранения неполадок подключения машин к Azure.

Сам агент является процессом daemon называется `himdsd` на `himds` Linux, и служба Windows называется на Windows.

При обычном `azcmagent connect` использовании используется для установления связи между `azcmagent disconnect` этой машиной и Azure, и если вы решите, что больше не хотите этого соединения. Другие команды для устранения неполадок или других специальных случаях.

## <a name="options"></a>Параметры

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>СМ. ТАКЖЕ

* [azcmagent Connect](#azcmagent-connect) - Подключает эту машину к Azure
* [azcmagent отключается](#azcmagent-disconnect) - отключает эту машину от Azure
* [azcmagent reconnect](#azcmagent-reconnect) - Воссоединяет эту машину к Azure
* [azcmagent show](#azcmagent-show) - Получает метаданные машины и статус агента. Это в первую очередь полезно для устранения неполадок.
* [azcmagent версия](#azcmagent-version) - Отображение гибридной версии агента управления

## <a name="azcmagent-connect"></a>azcmagent подключить

Подключает эту машину к Azure

### <a name="synopsis"></a>Краткий обзор

Создает ресурс в Azure, представляющий эту машину.

При этом используются параметры аутентификации, предоставляемые для создания ресурса в Azure Resource Manager, представляющем эту машину. Ресурс находится в запрашиваемой группе подписчиков и ресурсов, а данные о машине хранятся в регионе Azure, указанном параметром местоположения.
Имя ресурса по умолчанию — это хост-имя этой машины, если она не переопределена.

Сертификат, соответствующий Системе Назначенных Удостоверения данных этой машины, затем загружается и хранится локально. Как только этот шаг завершается, служба **метаданных связанных с Azure машин** и агент конфигурации гостей начинают синхронизироваться с облаком Azure.

Параметры аутентификации:

* Токен доступа`azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* Идентификатор и секрет службы`azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* Войд -14(Интерактивная)`azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

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

## <a name="azcmagent-disconnect"></a>отключение azcmagent

Отключает эту машину от Azure

### <a name="synopsis"></a>Краткий обзор

Удаляет ресурс в Azure, представляющий этот сервер.

Эта команда использует параметры аутентификации, предоставляемые для удаления ресурса Azure Manager, представляющего эту машину. После этого отключат **службу метаданных подключенных машин Azure** и агента конфигурации гостей. Эта команда не останавливает и не удаляет службы: удалите пакет для этого.

Эта команда требует более высоких привилегий, чем роль "Azure Connected Machine Onboarding".

После отключения машины используйте, `azcmagent connect` `azcmagent reconnect` если вы хотите создать новый ресурс для нее в Azure.

Параметры аутентификации:

* Токен доступа`azcmagent disconnect --access-token <>`
* Идентификатор и секрет службы`azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Интерактивное устройство войти`azcmagent disconnect --tenant-id <>`

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

## <a name="azcmagent-reconnect"></a>ацкмагент воссоединение

Повторное подключение этой машины к Azure

### <a name="synopsis"></a>Краткий обзор

Воссоединение машины с недействительными учетными данными в Azure.

Если у машины уже есть ресурс в Azure, но она не в состоянии проверить его подлинность, она может быть восстановлена с помощью этой команды. Это возможно, если машина была выключена достаточно долго для истечения срока действия сертификата (не менее 45 дней).

Если машина была отключена `azcmagent disconnect`с, вместо этого используйте. `azcmagent connect`

Эта команда использует параметры аутентификации, предоставляемые для получения новых учетных данных, соответствующих ресурсу Azure Resource Manager, представляющему эту машину.

Эта команда требует более высоких привилегий, чем роль **onboarding Azure Connected Machine.**

Параметры аутентификации

* Токен доступа`azcmagent reconnect --access-token <>`
* Идентификатор и секрет службы`azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* Интерактивное устройство войти`azcmagent reconnect --tenant-id <>`

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

## <a name="azcmagent-show"></a>azcmagent шоу

Получает метаданные машины и статус агента. Это в первую очередь полезно для устранения неполадок.

### <a name="synopsis"></a>Краткий обзор

Получает метаданные машины и статус агента. Это в первую очередь полезно для устранения неполадок.


### <a name="syntax"></a>Синтаксис

```
azcmagent show [flags]
```

### <a name="options"></a>Параметры

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent версия

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
