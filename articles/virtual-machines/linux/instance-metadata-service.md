---
title: Служба метаданных экземпляров Azure
description: Интерфейс RESTFUL для получения сведений о выпусках виртуальных машин Linux, а именно о событиях обслуживания, сети и предстоящих событий.
services: virtual-machines-linux
documentationcenter: ''
author: KumariSupriya
manager: paulmey
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 0e04f7e190ef22fb5c2b288e478cac5ffaf89141
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962515"
---
# <a name="azure-instance-metadata-service"></a>Служба метаданных экземпляров Azure

Служба метаданных экземпляров Azure (IMDS) предоставляет сведения о текущих экземплярах виртуальных машин и может использоваться для управления виртуальными машинами и их настройки.
Предоставленные сведения включают в себя номера SKU, конфигурации сети и будущие события обслуживания. Полный список доступных данных см. в разделе [API метаданных](#metadata-apis).

Служба метаданных экземпляров Azure — это конечная точка REST, доступная для всех виртуальных машин IaaS, созданных с помощью нового [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).
Конечная точка доступна по известному IP-адресу без поддержки маршрутизации (`169.254.169.254`), к которому можно получить доступ только в пределах виртуальной машины.

> [!IMPORTANT]
> Эта служба является **общедоступной** во всех регионах Azure.  Она регулярно получает обновления, чтобы предоставлять новые сведения об экземплярах виртуальной машины. На этой странице представлены актуальные [интерфейсы API метаданных](#metadata-apis) .

## <a name="service-availability"></a>Доступность служб

Служба доступна в общедоступных регионах Azure. Не все версии API-интерфейса могут быть доступны во всех регионах Azure.

Регионы                                        | Доступность?                                 | Поддерживаемые версии
-----------------------------------------------|-----------------------------------------------|-----------------
[Все общедоступные глобальные регионы Azure](https://azure.microsoft.com/regions/)     | Общедоступная версия | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15, 2019-11-01
[Azure для государственных организаций](https://azure.microsoft.com/overview/clouds/government/)              | Общедоступная версия | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15, 2019-11-01
[Azure China 21Vianet](https://www.azure.cn/)                                            | Общедоступная версия | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15, 2019-11-01
[Azure для Германии](https://azure.microsoft.com/overview/clouds/germany/)                    | Общедоступная версия | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15, 2019-11-01

Эта таблица обновляется при наличии обновлений служб и/или новых поддерживаемых версий.

Чтобы проверить службу метаданных экземпляров, создайте виртуальную машину в [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) или на [портале Azure](https://portal.azure.com) в приведенных выше регионах согласно примерам ниже.
Дополнительные примеры запросов IMDS можно найти в [примерах метаданных экземпляра Azure](https://github.com/microsoft/azureimds) .

## <a name="usage"></a>Использование

### <a name="versioning"></a>Управление версиями

Служба метаданных экземпляра имеет версию, и указание версии API в HTTP-запросе является обязательным.

Вы можете увидеть последние версии, перечисленные в этой [таблице доступности](#service-availability).

Так как мы добавляем новые версии, вы все еще можете получить доступ к предыдущим версиям для обеспечения совместимости, если используемый скрипт зависит от конкретных форматов данных.

Если версия не указана, возвращается ошибка со списком последних поддерживаемых версий.

> [!NOTE]
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Ответ**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

### <a name="using-headers"></a>Использование заголовков

При запросе службы метаданных экземпляров необходимо указать заголовок `Metadata: true`, чтобы избежать случайного перенаправления запроса.

### <a name="retrieving-metadata"></a>Получение метаданных

Используйте метаданные экземпляров для запуска виртуальных машин, созданных или управляемых с помощью [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Вы можете получить доступ ко всем категориям данных экземпляров виртуальной машины с помощью следующего запроса:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Во всех запросах метаданных экземпляров учитывается регистр.

### <a name="data-output"></a>Выходные данные

По умолчанию служба метаданных экземпляров возвращает данные в формате JSON (`Content-Type: application/json`). Однако при необходимости другие API-интерфейсы могут возвращать данные в различных форматах.
В следующей таблице приведены сведения о других форматах данных, поддерживаемых API-интерфейсами.

API | Формат данных по умолчанию | Другие форматы
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | none
/attested | json | none

Для доступа к нестандартному формату ответа укажите запрошенный формат в качестве параметра строки запроса. Пример.

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Для конечных узлов `format=json` не работает. Для этих запросов `format=text` необходимо явно указать, если форматом по умолчанию является JSON.

### <a name="security"></a>Безопасность

Конечная точка службы метаданных экземпляров доступна только в пределах запущенного экземпляра виртуальной машины на IP-адресе, не поддерживающем маршрутизацию. Кроме того, любой запрос с заголовком `X-Forwarded-For` отклоняется службой.
Чтобы гарантировать, что фактический запрос отправлен напрямую и не был получен в ходе непреднамеренного перенаправления, он должен содержать отправляемый заголовок `Metadata: true`.

### <a name="error"></a>Ошибка

Если элемент данных не найден или запрос неправильно сформирован, службы метаданных экземпляров возвращают стандартные ошибки HTTP. Пример.

Код состояния HTTP | Причина
----------------|-------
200 ОК |
400 — недопустимый запрос | Отсутствует заголовок `Metadata: true` или отсутствует формат при запросе конечного узла
Ошибка 404 — страница не найдена | Запрашиваемый элемент не существует
405 — недопустимый метод | Поддерживаются только запросы `GET`
429 — слишком много запросов | Сейчас API-интерфейс поддерживает максимум 5 запросов в секунду
500 — ошибка службы     | Повторите попытку через некоторое время

### <a name="examples"></a>Примеры

> [!NOTE]
> Все ответы API — это строки в формате JSON. Все приведенные ниже примеры ответов печатаются для удобства чтения.

#### <a name="retrieving-network-information"></a>Получение сведений о сети

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Ответ**

> [!NOTE]
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Получение общедоступного IP-адреса

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Получение всех метаданных для экземпляра

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**Ответ**

> [!NOTE]
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Получение метаданных в виртуальной машине Windows

**Запрос**

Метаданные экземпляра в Windows можно получить с помощью служебной программы `curl` службы PowerShell. 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content
```

Либо с помощью командлета `Invoke-RestMethod`:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-06-01 -Method get 
```

**Ответ**

> [!NOTE]
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>API метаданных

В конечной точке метаданных доступны следующие API:

Данные | Description | Представленные версии
-----|-------------|-----------------------
attested | См. раздел [Аттестованные данные](#attested-data). | 2018-10-01
удостоверение | Управляемые удостоверения для ресурсов Azure. Ознакомьтесь с [получением маркера доступа](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md). | 2018-02-01
instance | См. [API экземпляра](#instance-api) | 2017-04-02
scheduledevents | Ознакомьтесь со статьей [Служба метаданных Azure. Запланированные события (предварительная версия) для виртуальных машин Windows](scheduled-events.md) | 2017-08-01

#### <a name="instance-api"></a>API экземпляра

Следующие категории вычислений доступны через API экземпляра:

> [!NOTE]
> Через конечную точку метаданных доступ к следующим категориям осуществляется через экземпляр или вычислительные ресурсы.

Данные | Description | Представленные версии
-----|-------------|-----------------------
азенвиронмент | Среда Azure, в которой выполняется виртуальная машина | 2018-10-01
Пользовательские | Эта функция сейчас отключена, и мы будем обновлять эту документацию, когда она станет доступной | 2019-02-01
location | Регион Azure, в котором запущена виртуальная машина | 2017-04-02
name | Имя виртуальной машины. | 2017-04-02
offer | Сведения о предложении для образа виртуальной машины и имеются только для образов, развернутых из коллекции образов Azure. | 2017-04-02
osType | Windows или Linux | 2017-04-02
placementGroupId | [Группа размещения](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) масштабируемого набора виртуальных машин | 2017-08-01
План | [План](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) , содержащий имя, продукт и издателя для виртуальной машины, если это образ Azure Marketplace | 2018-04-02
platformUpdateDomain |  [Домен обновления](manage-availability.md), на котором запущена виртуальная машина | 2017-04-02
platformFaultDomain | [Домен сбоя](manage-availability.md), на котором запущена виртуальная машина | 2017-04-02
поставщик | Поставщик виртуальной машины | 2018-10-01
publicKeys | [Коллекция открытых ключей](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) , назначенных виртуальной машине и путям | 2018-04-02
publisher | Издатель образа виртуальной машины | 2017-04-02
имя_группы_ресурсов | [Группа ресурсов](../../azure-resource-manager/management/overview.md) для виртуальной машины | 2017-08-01
resourceId | [Полный](https://docs.microsoft.com/rest/api/resources/resources/getbyid) идентификатор ресурса | 2019-03-11
sku | Определенный номер SKU для образа виртуальной машины | 2017-04-02
storageProfile | См. раздел [профиль хранилища](#storage-profile) | 2019-06-01
subscriptionId | Подписка Azure для виртуальной машины | 2017-08-01
tags | [Теги](../../azure-resource-manager/management/tag-resources.md) для виртуальной машины  | 2017-08-01
тагслист | Теги форматируются в виде массива JSON для упрощения программного анализа  | 2019-06-04
version | Версия образа виртуальной машины | 2017-04-02
vmId | [Уникальный идентификатор](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) для виртуальной машины | 2017-04-02
vmScaleSetName | [Имя масштабируемого набора виртуальных машин](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) для масштабируемого набора виртуальных машин | 2017-12-01
vmSize | [Размер виртуальной машины](sizes.md) | 2017-04-02
зона | [Зона доступности](../../availability-zones/az-overview.md) виртуальной машины | 2017-12-01

Через API экземпляра можно использовать следующие категории сети:

> [!NOTE]
> Через конечную точку метаданных доступ к следующим категориям осуществляется через экземпляр, сеть или интерфейс.

Данные | Description | Представленные версии
-----|-------------|-----------------------
ipv4/privateIpAddress | Локальный IPv4-адрес виртуальной машины | 2017-04-02
ipv4/publicIpAddress | Общедоступный IPv4-адрес виртуальной машины | 2017-04-02
subnet/address | Адрес подсети виртуальной машины | 2017-04-02
subnet/prefix | Префикс подсети, пример 24 | 2017-04-02
ipv6/ipAddress | Локальный IPv6-адрес виртуальной машины | 2017-04-02
macAddress | Mac-адрес виртуальной машины | 2017-04-02

## <a name="attested-data"></a>Аттестованные данные

В рамках сценария, обслуживаемого службой метаданных экземпляров, предоставляется гарантия того, что предоставленные данные поступают из Azure. Мы подписываем часть этой информации, чтобы гарантировать, что в Azureработает надлежащий образ Marketplace.

### <a name="example-attested-data"></a>Пример аттестованных данных

> [!NOTE]
> Все ответы API — это строки в формате JSON. Все следующие примеры ответов представлены в удобном для чтения формате.

 **Запрос**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Версия API является обязательным полем. Поддерживаемые версии API см. в [разделе Доступность службы](#service-availability) .
Nonce — это необязательная строка из 10 цифр. Если не указано, IMDS возвращает текущую метку времени в формате UTC. Из-за механизма кэширования IMDS может возвращаться ранее кэшированное значение nonce.

 **Ответ**

> [!NOTE]
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Большой двоичный объект подписи является версией документа с подписью [pkcs7](https://aka.ms/pkcs7). Он содержит сертификат, используемый для подписывания, а также сведения о виртуальной машине, такие как vmId, SKU, nonce, subscriptionId, timeStamp для создания и окончания срока действия документа, а также сведения о плане образа. Сведения о плане указываются только для образов из Azure Marketplace. Можно извлечь сертификат из ответа и проверить с его помощью, является ли ответ допустимым и поступает ли он из Azure.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Получение аттестованных метаданных на виртуальной машине Windows

 **Запрос**

Метаданные экземпляра в Windows можно получить с помощью служебной программы `curl` службы PowerShell.

 ```powershell
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Либо с помощью командлета `Invoke-RestMethod`:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

Версия API является обязательным полем. Поддерживаемые версии API см. в разделе Доступность службы.
Nonce — это необязательная строка из 10 цифр. Если не указано, IMDS возвращает текущую метку времени в формате UTC. Из-за механизма кэширования IMDS может возвращаться ранее кэшированное значение nonce.

 **Ответ**

> [!NOTE]
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Большой двоичный объект подписи является версией документа с подписью [pkcs7](https://aka.ms/pkcs7). Он содержит сертификат, используемый для подписывания, а также сведения о виртуальной машине, такие как vmId, SKU, nonce, subscriptionId, timeStamp для создания и окончания срока действия документа, а также сведения о плане образа. Сведения о плане указываются только для образов из Azure Marketplace. Можно извлечь сертификат из ответа и проверить с его помощью, является ли ответ допустимым и поступает ли он из Azure.


## <a name="example-scenarios-for-usage"></a>Примеры сценариев использования  

### <a name="tracking-vm-running-on-azure"></a>Отслеживание виртуальной машины в Azure

Поставщику услуг может потребоваться отслеживать количество виртуальных машин, использующих ваше программное обеспечение, или установить агенты, отслеживающие уникальность виртуальной машины. Чтобы получить уникальный идентификатор для виртуальной машины, используйте поле `vmId` службы метаданных экземпляров.

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Ответ**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Размещение контейнеров и секций данных на основе домена сбоя или обновления

В некоторых сценариях размещение разных реплик имеет первостепенное значение. Например, для [размещения реплики HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) или размещения контейнера с помощью [оркестратора](https://kubernetes.io/docs/user-guide/node-selection/) необходимо знать домен сбоя `platformFaultDomain` и домен обновления `platformUpdateDomain`, на которых запущена виртуальная машина.
Кроме того, для принятия этих решений можно применить [Зоны доступности](../../availability-zones/az-overview.md) для экземпляров.
Вы можете запросить данные непосредственно через службу метаданных экземпляров.

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Ответ**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Получение дополнительных сведений о виртуальной машине при обращении в службу поддержки

Чтобы получить дополнительные сведения о виртуальной машине, поставщик услуг может позвонить в службу поддержки. Если клиент предоставит сведения о метаданных вычислений, специалист службы поддержки получит основные сведения о виртуальной машине в Azure.

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Ответ**

> [!NOTE]
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Начало работы среды Azure, где запущена виртуальная машина

Azure имеет различные национальные облака, например [Azure для государственных организаций](https://azure.microsoft.com/overview/clouds/government/). Иногда требуется, чтобы решения касательно среды выполнения принимала среда Azure. В следующем примере показано, как добиться такого поведения.

**Запрос**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Ответ**
```bash
AzurePublicCloud
```
Облако и значения среды Azure перечислены ниже.

 В облаке   | Среда Azure
---------|-----------------
[Все общедоступные глобальные регионы Azure](https://azure.microsoft.com/regions/)     | азурепубликклауд
[Azure для государственных организаций](https://azure.microsoft.com/overview/clouds/government/)              | азуреусговернментклауд
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud;
[Azure для Германии](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>Получение тегов для виртуальной машины

К виртуальной машине Azure могут быть применены теги для логической организации их в таксономию. Теги, назначенные виртуальной машине, можно получить с помощью приведенного ниже запроса.

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Ответ**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags` поле — это строка с тегами, разделенная точками с запятой. Это может быть проблемой, если в самих тегах используются точки с запятой. Если средство синтаксического анализа записывается для программного извлечения тегов, следует полагаться на поле `tagsList`, которое является массивом JSON без разделителей, и, следовательно, проще проанализировать.

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=json"
```

**Ответ**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

### <a name="validating-that-the-vm-is-running-in-azure"></a>Проверка того, что виртуальная машина запущена в Azure

Поставщикам Marketplace часто нужно гарантировать, что их программное обеспечение лицензируется только для запуска из Azure. Если кто-то копирует виртуальный жесткий диск в локальную среду, он должен иметь возможность обнаруживать это. Обращение к службе метаданных экземпляров позволит поставщикам Marketplace получить подписанные данные, гарантированно поступающие из Azure.

> [!NOTE]
> Необходимо установить jq.

**Запрос**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Ответ**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
"sku": "RS3-Pro"
}
```

Данные | Description
-----|------------
nonce | Необязательная строка, предоставляемая пользователем вместе с запросом. Если значение nonce в запросе не указано, возвращается текущая метка времени в формате UTC.
План | [План](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) для виртуальной машины в образе Azure Marketplace содержит имя, а также сведения о продукте и издателе
timestamp/createdOn | Метка времени в формате UTC, в которую был создан первый подписанный документ
timestamp/expiresOn | Метка времени в формате UTC, с которой истекает срок действия подписанного документа
vmId |  [Уникальный идентификатор](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) для виртуальной машины
subscriptionId | Подписка Azure для виртуальной машины, представленная в `2019-04-30`
sku | Конкретный номер SKU для образа виртуальной машины, представленный в `2019-11-01`

#### <a name="verifying-the-signature"></a>Проверка подписи

Получив описанную выше подпись, вы можете убедиться, что она поступила от корпорации Майкрософт. Также вы можете проверить промежуточный сертификат и всю цепочку сертификатов. Наконец, можно проверить правильность идентификатора подписки.

> [!NOTE]
> Для общедоступного облака и национального облака используются разные сертификаты.

 В облаке | Сертификат
---------|-----------------
[Все общедоступные глобальные регионы Azure](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure для государственных организаций](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | metadata.azure.cn
[Azure для Германии](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

Если промежуточный сертификат не может быть загружен из-за сетевых ограничений во время проверки, можно закрепить промежуточный сертификат. Однако Azure будет переключать сертификаты в соответствии со стандартной практикой PKI. При переходе происходит необходимость обновления прикрепленных сертификатов. При каждом запланированном изменении промежуточного сертификата блог Azure будет обновлен, а клиенты Azure будут уведомлены об этом. Промежуточные сертификаты можно найти [здесь](https://www.microsoft.com/pki/mscorp/cps/default.htm). Промежуточные сертификаты для каждого региона могут отличаться.

### <a name="failover-clustering-in-windows-server"></a>Отказоустойчивая кластеризация в Windows Server

В некоторых ситуациях при запросе службы метаданных экземпляров с отказоустойчивой кластеризацией необходимо добавить маршрут в таблицу маршрутизации.

1. Откройте окно командной строки с правами администратора.

2. Выполните следующую команду и запишите адрес интерфейса для сети назначения (`0.0.0.0`) в таблице маршрутизации IPv4.

```bat
route print
```

> [!NOTE] 
> Следующий пример выходных данных из виртуальной машины Windows Server с поддержкой отказоустойчивого кластера для удобства содержит только таблицу маршрутизации IPv4.

```bat
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. Выполните следующую команду и используйте адрес интерфейса для сети назначения (`0.0.0.0`), то есть (`10.0.1.10`) в этом примере.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="storage-profile"></a>Профиль хранилища

Служба метаданных экземпляров может предоставлять сведения о дисках хранилища, связанных с виртуальной машиной. Эти данные можно найти в конечной точке экземпляра, COMPUTE/storageProfile.

Профиль хранилища виртуальной машины делится на три категории: ссылки на образ, диск ОС и диски данных.

Объект ссылки на изображение содержит следующие сведения о образе ОС:

Данные    | Description
--------|-----------------
идентификатор      | Идентификатор ресурса
offer   | Предложение образа платформы или Marketplace
publisher | Издатель образа.
sku     | Номер SKU образа
version | Версия образа платформы или Marketplace

Объект диска ОС содержит следующие сведения о диске ОС, используемом виртуальной машиной.

Данные    | Description
--------|-----------------
кэширующий | Требования к кэшированию
createOption | Сведения о создании виртуальной машины
диффдисксеттингс | Параметры временного диска
дисксизегб | Размер диска в ГБ
изображение   | Виртуальный жесткий диск исходного образа пользователя
номеров     | Номер логического устройства диска
манажеддиск | Параметры управляемого диска
name    | Имя диска
VHD     | Виртуальный жесткий диск
вритеакцелераторенаблед | Включено ли на диске writeAccelerator или нет

Массив дисков данных содержит список дисков данных, подключенных к виртуальной машине. Каждый объект диска данных содержит следующие сведения:

Данные    | Description
--------|-----------------
кэширующий | Требования к кэшированию
createOption | Сведения о создании виртуальной машины
диффдисксеттингс | Параметры временного диска
дисксизегб | Размер диска в ГБ
енкриптионсеттингс | Параметры шифрования для диска
изображение   | Виртуальный жесткий диск исходного образа пользователя
манажеддиск | Параметры управляемого диска
name    | Имя диска
osType  | Тип ОС, включаемой в диск
VHD     | Виртуальный жесткий диск
вритеакцелераторенаблед | Включено ли на диске writeAccelerator или нет

Ниже приведен пример запроса сведений о хранилище виртуальной машины.

**Запрос**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Ответ**

> [!NOTE]
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Примеры вызова службы метаданных с использованием различных языков в виртуальной машине

Язык | Пример
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>Часто задаваемые вопросы

1. Я получаю ошибку `400 Bad Request, Required metadata header not specified`. Что это означает?
   * Для работы службы метаданных экземпляров заголовок `Metadata: true` необходимо передать в запрос. Передача заголовка в вызов REST позволяет получить доступ к службе метаданных экземпляров.
2. Почему я не получаю сведения о вычислениях для виртуальной машины?
   * Сейчас служба метаданных экземпляров поддерживает только экземпляры, созданные с помощью Azure Resource Manager. В будущем мы можем добавить поддержку виртуальных машин облачной службы.
3. Виртуальная машина была недавно создана с помощью Azure Resource Manager. Почему не отображаются сведения о метаданных вычислений?
   * Чтобы отобразить метаданные вычислений для любой виртуальной машины, созданной после сентября 2016 года, необходимо добавить [тег](../../azure-resource-manager/management/tag-resources.md). Для обновления метаданных более старых виртуальных машин (созданных до сентября 2016 г.) удалите расширения или диски данных или добавьте их к виртуальной машине.
4. Отображаются не все данные для новой версии.
   * Чтобы отобразить метаданные вычислений для любой виртуальной машины, созданной после сентября 2016 года, необходимо добавить [тег](../../azure-resource-manager/management/tag-resources.md). Для обновления метаданных более старых виртуальных машин (созданных до сентября 2016 г.) удалите расширения или диски данных или добавьте их к виртуальной машине.
5. Почему я получаю ошибку `500 Internal Server Error`?
   * Повторите запрос в зависимости от экспоненциальной системы. Если проблема не исчезла, обратитесь в службу поддержки Azure.
6. Где можно задать дополнительные вопросы или оставить комментарии?
   * Добавьте здесь свои комментарии здесь: https://feedback.azure.com.
7. Будет ли это работать для экземпляра масштабируемого набора виртуальных машин?
   * Да, служба метаданных доступна для экземпляров масштабируемого набора.
8. Как можно получить поддержку для службы?
   * Чтобы получить поддержку для службы, сообщите о проблеме с виртуальной машиной на портале Azure, указав, что не смогли получить ответ метаданных после многих попыток.
9. Почему я получаю запрос о превышении интервала ожидания вызова службы?
   * Вызовы метаданных должны выполняться с основного IP-адреса, назначенного первичной сетевой карте виртуальной машины. Кроме того, если вы изменили маршруты, необходимо указать маршрут для 169.254.0.0/16-адреса из сетевой карты.
10. Теги в наборе масштабирования виртуальных машин обновлены. Почему они не отображаются в экземплярах, в отличие от виртуальных машин?
    * В настоящее время теги ScaleSets отображаются только в виртуальной машине при перезагрузке, переименовании или замене диска экземпляра.

    ![Поддержка метаданных экземпляров](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Дальнейшие действия

- См. дополнительные сведения о [запланированных событиях](scheduled-events.md)
