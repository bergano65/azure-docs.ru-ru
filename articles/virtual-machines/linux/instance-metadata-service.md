---
title: Служба метаданных экземпляров Azure
description: Интерфейс RESTFUL для получения сведений о событиях по вычислению, сети и предстоящему обслуживанию виртуальных машин.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: bb9bc978e49cddab13ab1e4f7ec4f0b74d369ac1
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705849"
---
# <a name="azure-instance-metadata-service-imds"></a>Служба метаданных экземпляров Azure (IMDS)

Служба метаданных экземпляров Azure (IMDS) предоставляет сведения о текущем выполнении экземпляров виртуальных машин, которые можно использовать для настройки виртуальных машин и управления ими.
Эти сведения включают SKU, хранилище, конфигурации сети и будущие события обслуживания. Полный список доступных данных см. в разделе [API метаданных](#metadata-apis).
Служба метаданных экземпляра доступна для запуска экземпляров виртуальных машин и масштабируемых наборов виртуальных машин. Все API-интерфейсы поддерживают виртуальные машины, созданные или управляемые с помощью [Azure Resource Manager](/rest/api/resources/). Только конечные точки аттестации и сети поддерживают классические виртуальные машины (не ARM), а аттестация — только в ограниченном экстенте.

IMDS Azure — это конечная точка RESTFUL, доступная по известному ненаправляемому IP-адресу ( `169.254.169.254` ), доступ к которой можно получить только из виртуальной машины. Обмен данными между виртуальной машиной и IMDS никогда не покидает узел.
Рекомендуется, чтобы клиенты HTTP обрабатывали веб-прокси в виртуальной машине при запросе IMDS и считались `169.254.169.254` такими же, как [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Безопасность

Конечная точка службы метаданных экземпляров доступна только в пределах запущенного экземпляра виртуальной машины на IP-адресе, не поддерживающем маршрутизацию. Кроме того, любой запрос с заголовком `X-Forwarded-For` отклоняется службой.
Чтобы гарантировать, что фактический запрос отправлен напрямую и не был получен в ходе непреднамеренного перенаправления, он должен содержать отправляемый заголовок `Metadata: true`.

> [!IMPORTANT]
> Служба метаданных экземпляров не является каналом для конфиденциальных данных. Конечная точка открыта для всех процессов на виртуальной машине. Сведения, предоставляемые посредством этой службы, следует рассматривать как общие сведения для всех приложений, запущенных на виртуальной машине.

## <a name="usage"></a>Использование

### <a name="accessing-azure-instance-metadata-service"></a>Доступ к службе метаданных экземпляров Azure

Чтобы получить доступ к службе метаданных экземпляров, создайте виртуальную машину в [Azure Resource Manager](/rest/api/resources/) или на [портале Azure](https://portal.azure.com) согласно примерам ниже.
Дополнительные примеры запросов к IMDS можно найти в разделе [Примеры метаданных экземпляра Azure](https://github.com/microsoft/azureimds).

Ниже приведен пример кода для получения всех метаданных из экземпляра, что позволяет получить доступ к определенному источнику данных. См. раздел [API метаданных](#metadata-apis). 

**Запрос**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-06-01"
```

**Ответ**

> [!NOTE]
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }],
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
                    "id": "/subscriptions/8d10da13-8125-4ba9-a717-bf7490507b3d/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
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
        "subscriptionId": "8d10da13-8125-4ba9-a717-bf7490507b3d",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    }
}
```

### <a name="data-output"></a>Выходные данные

По умолчанию служба метаданных экземпляров возвращает данные в формате JSON (`Content-Type: application/json`). Однако при запросе некоторые API-интерфейсы могут возвращать данные в различных форматах.
В следующей таблице приведены сведения о других форматах данных, поддерживаемых API-интерфейсами.

API | Формат данных по умолчанию | Другие форматы
--------|---------------------|--------------
/attested | json | none
/Identity | json | none
/instance | json | text
/scheduledevents | json | none

Для доступа к нестандартному формату ответа укажите запрошенный формат в качестве параметра строки запроса. Пример:

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Для конечных узлов в/Метадата/инстанце `format=json` не работает. Для этих запросов необходимо `format=text` явно указать, так как форматом по умолчанию является JSON.

### <a name="versioning"></a>Управление версиями

Указание версии службы метаданных экземпляра и версии API в HTTP-запросе является обязательным.

Поддерживаемые версии API: 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 01.08.2019
- 2019-08-15
- 2019-11-01
- 2020-06-01

Обратите внимание, что выпуск новой версии для развертывания во всех регионах может занять некоторое время.

Так как мы добавляем новые версии, вы все еще можете получить доступ к предыдущим версиям для обеспечения совместимости, если используемый скрипт зависит от конкретных форматов данных.

Если версия не указана, возвращается ошибка со списком актуальных поддерживаемых версий.

> [!NOTE]
> Ответ представляет собой строку JSON. В следующем примере показана ошибка, отображаемая в том случае, если версия не указана. В целях удобочитаемости ответ приведен в структурированном виде.

**Запрос**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance"
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

## <a name="metadata-apis"></a>API метаданных

Служба метаданных содержит несколько интерфейсов API, представляющих различные источники данных.

API | Описание | Представленные версии
----|-------------|-----------------------
/attested | См. раздел [Аттестованные данные](#attested-data). | 2018-10-01
/Identity | См. раздел [Получение маркера доступа](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | См. раздел [API экземпляра](#instance-api) | 2017-04-02
/scheduledevents | Ознакомьтесь со статьей [Служба метаданных Azure. Запланированные события (предварительная версия) для виртуальных машин Windows](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>API экземпляра

API экземпляра предоставляет важные метаданные для экземпляров виртуальных машин, включая виртуальную машину, сеть и хранилище. Доступ к следующим категориям можно получить с помощью экземпляра или вычислений.

Данные | Описание | Представленные версии
-----|-------------|-----------------------
azEnvironment | Среда Azure, где запущена виртуальная машина | 2018-10-01
customData | Эта функция сейчас отключена. Мы будем обновлять эту документацию, когда она станет доступной | 2019-02-01
ишосткомпатибилитилайервм | Определяет, выполняется ли виртуальная машина на уровне совместимости узла | 2020-06-01
location | Регион Azure, в котором запущена виртуальная машина | 2017-04-02
name | Имя виртуальной машины. | 2017-04-02
offer | Предоставление сведений для образа виртуальной машины (эти значения доступны только для образов, развернутых из коллекции образов Azure) | 2017-04-02
osType | Windows или Linux | 2017-04-02
placementGroupId | [Группа размещения](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) масштабируемого набора виртуальных машин | 2017-08-01
План | [План](/rest/api/compute/virtualmachines/createorupdate#plan), указывающий наименование, продукт и издателя виртуальной машины (если используется образ Azure Marketplace) | 2018-04-02
platformUpdateDomain |  [Домен обновления](manage-availability.md), на котором запущена виртуальная машина | 2017-04-02
platformFaultDomain | [Домен сбоя](manage-availability.md), на котором запущена виртуальная машина | 2017-04-02
поставщик | Поставщик виртуальной машины | 2018-10-01
publicKeys | [Коллекция открытых ключей](/rest/api/compute/virtualmachines/createorupdate#sshpublickey), назначенная виртуальной машине и путям | 2018-04-02
publisher | Издатель образа виртуальной машины | 2017-04-02
имя_группы_ресурсов | [Группа ресурсов](../../azure-resource-manager/management/overview.md) для виртуальной машины | 2017-08-01
resourceId | [Полный](/rest/api/resources/resources/getbyid) идентификатор ресурса | 2019-03-11
sku | Определенный номер SKU для образа виртуальной машины | 2017-04-02
Секуритипрофиле. secureBootEnabled | Определяет, включена ли безопасная Загрузка UEFI на виртуальной машине | 2020-06-01
Секуритипрофиле. Виртуалтпменаблед | Определяет, включен ли на виртуальной машине виртуальный доверенный платформенный модуль (TPM) (TPM) | 2020-06-01
storageProfile | См. раздел [Профиль службы хранилища](#storage-metadata) | 2019-06-01
subscriptionId | Подписка Azure для виртуальной машины | 2017-08-01
tags | [Теги](../../azure-resource-manager/management/tag-resources.md) для виртуальной машины  | 2017-08-01
tagsList | Теги форматируются в виде массива JSON для упрощения программного анализа  | 2019-06-04
version | Версия образа виртуальной машины | 2017-04-02
vmId | [Уникальный идентификатор](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) для виртуальной машины | 2017-04-02
vmScaleSetName | [Имя масштабируемого набора виртуальных машин](../../virtual-machine-scale-sets/overview.md) для масштабируемого набора виртуальных машин | 2017-12-01
vmSize | [Размер виртуальной машины](../sizes.md) | 2017-04-02
зона | [Зона доступности](../../availability-zones/az-overview.md) виртуальной машины | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>Пример 1 Отслеживание виртуальной машины в Azure

Поставщику услуг может потребоваться отслеживать количество виртуальных машин, использующих ваше программное обеспечение, или установить агенты, отслеживающие уникальность виртуальной машины. Чтобы получить уникальный идентификатор для виртуальной машины, используйте поле `vmId` службы метаданных экземпляров.

**Запрос**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Ответ**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-containers-data-partitions-based-faultupdate-domain"></a>Пример 2 Размещение контейнеров и секций данных на основе домена сбоя или обновления

В некоторых сценариях размещение разных реплик имеет первостепенное значение. Например, для [размещения реплики HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) или размещения контейнера с помощью [оркестратора](https://kubernetes.io/docs/user-guide/node-selection/) необходимо знать домен сбоя `platformFaultDomain` и домен обновления `platformUpdateDomain`, на которых запущена виртуальная машина.
Кроме того, для принятия этих решений можно применить [Зоны доступности](../../availability-zones/az-overview.md) для экземпляров.
Вы можете запросить данные непосредственно через службу метаданных экземпляров.

**Запрос**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Ответ**

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>Пример 3. Получение дополнительных сведений о виртуальной машине при обращении в службу поддержки

Чтобы получить дополнительные сведения о виртуальной машине, поставщик услуг может позвонить в службу поддержки. Если клиент предоставит сведения о метаданных вычислений, специалист службы поддержки получит основные сведения о виртуальной машине в Azure.

**Запрос**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
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

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>Пример 4. Начало работы среды Azure, где запущена виртуальная машина

Azure имеет различные национальные облака, например [Azure для государственных организаций](https://azure.microsoft.com/overview/clouds/government/). Иногда требуется, чтобы решения касательно среды выполнения принимала среда Azure. В следующем примере показано, как добиться такого поведения.

**Запрос**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Ответ**

```text
AzurePublicCloud
```

Облако и значения среды Azure приведены ниже.

 Cloud   | Среда Azure
---------|-----------------
[Все общедоступные глобальные регионы Azure](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure для государственных организаций](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud;
[Azure для Германии](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Метаданные сети 

Метаданные сети являются частью API экземпляра. Следующие категории сети доступны посредством конечной точки экземпляра/сети.

Данные | Описание | Представленные версии
-----|-------------|-----------------------
ipv4/privateIpAddress | Локальный IPv4-адрес виртуальной машины | 2017-04-02
ipv4/publicIpAddress | Общедоступный IPv4-адрес виртуальной машины | 2017-04-02
subnet/address | Адрес подсети виртуальной машины | 2017-04-02
subnet/prefix | Префикс подсети, пример 24 | 2017-04-02
ipv6/ipAddress | Локальный IPv6-адрес виртуальной машины | 2017-04-02
macAddress | Mac-адрес виртуальной машины | 2017-04-02

> [!NOTE]
> Все ответы API — это строки в формате JSON. Все следующие примеры ответов в целях удобочитаемости представлены в структурированном виде.

#### <a name="sample-1-retrieving-network-information"></a>Пример 1 Получение сведений о сети

**Запрос**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
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

#### <a name="sample-2-retrieving-public-ip-address"></a>Пример 2 Получение общедоступного IP-адреса

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Метаданные службы хранилища

Метаданные службы хранилища являются частью API экземпляра в конечной точке экземпляра/вычисления/профиля службы хранилища.
Он предоставляет сведения о дисках хранилища, связанных с виртуальной машиной. 

Профиль хранилища виртуальной машины делится на три категории: ссылка на образ, диск ОС и диски данных.

Объект ссылки на изображение содержит следующие сведения об образе ОС.

Данные    | Описание
--------|-----------------
идентификатор      | Идентификатор ресурса
offer   | Предоставление образа платформы или Marketplace
publisher | Издатель образа.
sku     | Номер SKU образа
version | Версия образа платформы или Marketplace

Объект диска ОС содержит следующие сведения о диске ОС, который используется виртуальной машиной.

Данные    | Описание
--------|-----------------
caching | Требования к кэшированию
createOption | Сведения о способе создания виртуальной машины
diffDiskSettings | Параметры временного диска
diskSizeGB | Размер диска (ГБ)
Изображение   | Виртуальный жесткий диск исходного образа пользователя
lun     | Номер логического устройства диска (LUN)
managedDisk | Параметры управляемого диска
name    | Имя диска
vhd     | Виртуальный жесткий диск
writeAcceleratorEnabled | Включен ли параметр writeAccelerator на диске

Массив дисков данных содержит список дисков данных, подключенных к виртуальной машине. Каждый объект диска данных содержит следующие сведения.

Данные    | Описание
--------|-----------------
caching | Требования к кэшированию
createOption | Сведения о способе создания виртуальной машины
diffDiskSettings | Параметры временного диска
diskSizeGB | Размер диска (ГБ)
encryptionSettings | Параметры шифрования для диска
Изображение   | Виртуальный жесткий диск исходного образа пользователя
managedDisk | Параметры управляемого диска
name    | Имя диска
osType  | Тип ОС, добавленной на диск
vhd     | Виртуальный жесткий диск
writeAcceleratorEnabled | Включен ли параметр writeAccelerator на диске

В следующем примере показано, как запросить сведения о хранилище виртуальной машины.

**Запрос**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
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

## <a name="vm-tags"></a>Теги виртуальной машины

Теги виртуальных машин включены в API экземпляра в конечной точке экземпляра, вычислений или тегов.
К виртуальной машине Azure можно применить теги, чтобы логически организовать их в таксономию. Теги, назначенные виртуальной машине, можно получить с помощью запроса ниже.

**Запрос**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Ответ**

```text
Department:IT;Environment:Test;Role:WebRole
```

Поле `tags` представляет собой строку с тегами, разделенными точкой с запятой. Эти выходные данные могут быть проблемой, если в самих тегах используются точки с запятой. Если средство синтаксического анализа написано для программного извлечения тегов, следует полагаться на `tagsList` поле. `tagsList`Поле является массивом JSON без разделителей и, следовательно, проще в синтаксическом анализе.

**Запрос**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04"
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

## <a name="attested-data"></a>Аттестованные данные

Один из сценариев работы службы метаданных экземпляров — предоставление гарантий, что предоставляемые данные действительно поступили из Azure. Мы подписываем часть этой информации, чтобы гарантировать, что в Azureработает надлежащий образ Marketplace.

### <a name="sample-1-getting-attested-data"></a>Пример 1 Пример аттестованных данных

> [!NOTE]
> Все ответы API — это строки в формате JSON. Все следующие примеры ответов представлены в удобном для чтения формате.

**Запрос**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

Поле api-version является обязательным. Поддерживаемые версии API см. в разделе [Использование](#usage).
Необязательное поле Nonce содержит 10-значное строковое значение. Если значение не указано, IMDS возвращает вместо него текущую метку времени в формате UTC.

> [!NOTE]
> Из-за механизма кэширования IMDS может возвращаться ранее кэшированное значение nonce.

**Ответ**

> [!NOTE]
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Большой двоичный объект подписи является версией документа с подписью [pkcs7](https://aka.ms/pkcs7). Он содержит сертификат, используемый для подписывания, а также определенные сведения, относящиеся к виртуальной машине. Для виртуальных машин ARM сюда входят vmId, SKU, nonce, subscriptionId, метка времени для создания и срока действия документа, а также сведения о плане образа. Сведения о плане заполняются только для образов из Azure Marketplace. Для классической (не ARM) виртуальной машины гарантируется заполнение только vmId. Можно извлечь сертификат из ответа и проверить с его помощью, является ли ответ допустимым и поступает ли он из Azure.
Документ содержит следующие поля:

Данные | Описание
-----|------------
nonce | Строка, которую можно дополнительно указать с помощью запроса. Если не указан nonce, используется текущая отметка времени в формате UTC
План | [План образа Azure Marketplace](/rest/api/compute/virtualmachines/createorupdate#plan). Содержит идентификатор плана (имя), образ продукта или предложение (продукт) и идентификатор издателя (издатель).
timestamp/createdOn | Метка времени в формате UTC для момента создания подписанного документа
timestamp/expiresOn | Метка времени в формате UTC для момента истечения срока действия подписанного документа
vmId |  [Уникальный идентификатор](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) для виртуальной машины
subscriptionId | Подписка Azure для виртуальной машины, представленная в `2019-04-30`
sku | Конкретный номер SKU для образа виртуальной машины, представленный в `2019-11-01`

> [!NOTE]
> Для классической (не ARM) виртуальной машины гарантируется заполнение только vmId.

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>Пример 2 Проверка того, что виртуальная машина запущена в Azure

Поставщикам Marketplace часто нужно гарантировать, что их программное обеспечение лицензируется только для запуска из Azure. Им требуется знать, если кто-то скопирует виртуальный жесткий диск в локальную среду. Обращение к службе метаданных экземпляров позволит поставщикам Marketplace получить подписанные данные, гарантированно поступающие из Azure.

> [!NOTE]
> Необходимо установить jq.

**Запрос**

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2019-04-30" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Ответ**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Убедитесь, что подпись относится к Microsoft Azure и проверьте цепочку сертификатов на наличие ошибок.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

> [!NOTE]
> Из-за механизма кэширования IMDS может возвращаться ранее кэшированное значение nonce.

Элемент nonce в подписанном документе можно сравнить, если в первоначальном запросе был указан параметр nonce.

> [!NOTE]
> Для общедоступного облака и национального облака используются разные сертификаты.

Cloud | Сертификат
------|------------
[Все общедоступные глобальные регионы Azure](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure для государственных организаций](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure для Германии](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> Известна ошибка, связанная с сертификатом, используемым для подписания. Сертификаты могут не иметь точного соответствия `metadata.azure.com` для общедоступного облака. Поэтому для проверки сертификата должно быть разрешено общее имя из любого поддомена `.metadata.azure.com`.

Если промежуточный сертификат нельзя загрузить из-за ограничений сети во время проверки, можно закрепить промежуточный сертификат. Однако Azure будет заменять сертификаты в соответствии со стандартными требованиями PKI. Закрепленные сертификаты потребуется обновить при замене. При каждом запланированном изменении или обновлении промежуточного сертификата также обновляется блог Azure, и заказчики получают соответствующее уведомление. Промежуточные сертификаты приведены [здесь](https://www.microsoft.com/pki/mscorp/cps/default.htm). Для разных регионов могут использоваться различные промежуточные сертификаты.

> [!NOTE]
> Промежуточный сертификат для Azure для Китая (21Vianet) будет получен из DigiCert Global Root CA вместо Baltimore.
Кроме того, если вы закрепили промежуточные сертификаты для Azure для Китая (21 Vianet) в рамках изменения центра сертификации корневых цепочек, необходимо обновить промежуточные сертификаты.

## <a name="managed-identity-via-metadata-service"></a>Управляемое удостоверение через службу метаданных

На виртуальной машине можно включить управляемое удостоверение, назначенное системой, или назначить одному или нескольким пользовательским управляемым удостоверениям для виртуальной машины.
Маркеры для управляемых удостоверений затем можно запросить из службы метаданных экземпляра. Эти токены можно использовать для проверки подлинности в других службах Azure, таких как Azure Key Vault.

Подробные инструкции по включению этой функции см. в разделе [Получение маркера доступа](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events-via-metadata-service"></a>Запланированные события через службу метаданных
Состояние запланированных событий можно получить с помощью службы метаданных, а затем пользователь сможет указать набор действий, выполняемых при возникновении этих событий.  Дополнительные сведения см. в разделе [Запланированные события](scheduled-events.md). 

## <a name="regional-availability"></a>Доступность по регионам

Служба **общедоступна** во всех облаках Azure.

## <a name="sample-code-in-different-languages"></a>Примеры кодов на разных языках

Примеры вызова службы метаданных с использованием различных языков в виртуальной машине.

Язык      | Пример
--------------|----------------
Bash          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Node.js        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="error-and-debugging"></a>Ошибка и отладка

Если элемент данных не найден или запрос неправильно сформирован, службы метаданных экземпляров возвращают стандартные ошибки HTTP. Пример:

Код состояния HTTP | Причина
----------------|-------
200 ОК |
400 — недопустимый запрос | Отсутствует `Metadata: true` заголовок или отсутствующий параметр `format=json` при запросе конечного узла
404 — не найдено | Запрашиваемый элемент не существует
405 — недопустимый метод | Поддерживаются только запросы `GET`
410 — потеряно | Подождите немного и повторите попытку в течение 70 секунд
429 — слишком много запросов | Сейчас API-интерфейс поддерживает максимум 5 запросов в секунду
500 — ошибка службы     | Повторите попытку через некоторое время

### <a name="known-issues-and-faq"></a>Известные проблемы и часто задаваемые вопросы

1. Я получаю ошибку `400 Bad Request, Required metadata header not specified`. Что это означает?
   * Для работы службы метаданных экземпляров заголовок `Metadata: true` необходимо передать в запрос. Передача заголовка в вызов REST позволяет получить доступ к службе метаданных экземпляров.
1. Почему я не получаю сведения о вычислениях для виртуальной машины?
   * Сейчас служба метаданных экземпляров поддерживает только экземпляры, созданные с помощью Azure Resource Manager. В будущем мы можем добавить поддержку виртуальных машин облачной службы.
1. Виртуальная машина была недавно создана с помощью Azure Resource Manager. Почему не отображаются сведения о метаданных вычислений?
   * Чтобы отобразить метаданные вычислений для любой виртуальной машины, созданной после сентября 2016 года, необходимо добавить [тег](../../azure-resource-manager/management/tag-resources.md). Для старых виртуальных машин (созданных до сентября 2016) добавьте или удалите расширения или диски данных для экземпляров виртуальных машин, чтобы обновить метаданные.
1. Отображаются не все данные для новой версии.
   * Чтобы отобразить метаданные вычислений для любой виртуальной машины, созданной после сентября 2016 года, необходимо добавить [тег](../../azure-resource-manager/management/tag-resources.md). Для старых виртуальных машин (созданных до сентября 2016) добавьте или удалите расширения или диски данных для экземпляров виртуальных машин, чтобы обновить метаданные.
1. Почему я получаю сообщение об ошибке `500 Internal Server Error` или `410 Resource Gone` ?
   * Повторите запрос на основе экспоненциальной системы или других методов, описанных в разделе [обработка временных сбоев](/azure/architecture/best-practices/transient-faults). Если проблема не исчезнет, создайте в портал Azure для виртуальной машины проблему поддержки.
1. Будет ли это работать для экземпляров масштабируемого набора виртуальных машин?
   * Для экземпляров масштабируемого набора доступна да служба метаданных.
1. Я обновил теги в масштабируемых наборах виртуальных машин, но они не отображаются в экземплярах, в отличие от виртуальных машин с одним экземпляром.
   * В настоящее время теги для масштабируемых наборов отображаются только для виртуальной машины при перезагрузке, повторном образе или изменении места на диске экземпляра.
1. Почему я получаю запрос о превышении интервала ожидания вызова службы?
   * Вызовы метаданных должны выполняться с основного IP-адреса, назначенного основной сетевой карте виртуальной машины. Кроме того, если вы изменили маршруты, в локальной таблице маршрутизации виртуальной машины должен быть маршрут для адреса 169.254.169.254/32.
   * <details>
        <summary>Проверка таблицы маршрутизации</summary>

        1. Выведите дамп локальной таблицы маршрутизации с помощью команды, например, `netstat -r` и найдите запись IMDS (например,):
            ```console
            ~$ netstat -r
            Kernel IP routing table
            Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
            default         _gateway        0.0.0.0         UG        0 0          0 eth0
            168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
            169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
            172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
            ```
        1. Убедитесь, что маршрут существует для `169.254.169.254` , и обратите внимание на соответствующий сетевой интерфейс (например, `eth0` ).
        1. Выведите дамп конфигурации интерфейса для соответствующего интерфейса в таблице маршрутизации (Обратите внимание, что может отличаться точное имя файла конфигурации).
            ```console
            ~$ cat /etc/netplan/50-cloud-init.yaml
            network:
            ethernets:
                eth0:
                    dhcp4: true
                    dhcp4-overrides:
                        route-metric: 100
                    dhcp6: false
                    match:
                        macaddress: 00:0d:3a:e4:c7:2e
                    set-name: eth0
            version: 2
            ```
        1. При использовании динамического IP-адреса запишите MAC-адрес. Если вы используете статический IP-адрес, вы можете заметить указанные IP-адреса (s) и/или MAC.
        1. Убедитесь, что интерфейс соответствует основному сетевому адаптеру виртуальной машины и основному IP-адресу. Основной сетевой адаптер или IP-адрес можно найти, просмотрев конфигурацию сети на портале Azure или выполнив поиск по [Azure CLI](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-show). Обратите внимание на общедоступный и частный IP-адреса (и MAC-адрес при использовании интерфейса командной строки). Пример PowerShell CLI:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: ipexample606 True 00-0D-3A-E4-C7-2E
            ```
        1. Если они не совпадают, обновите таблицу маршрутизации таким путем, чтобы основной сетевой адаптер или IP-адрес был целевым.
    </details>

## <a name="support-and-feedback"></a>Поддержка и обратная связь

Отправьте свои отзывы и комментарии по адресу https://feedback.azure.com .

Чтобы получить поддержку для службы, сообщите о проблеме с виртуальной машиной на портале Azure, указав, что не смогли получить ответ метаданных после многих попыток.
Используйте тип проблемы `Management` и выберите в `Instance Metadata Service` качестве категории.

![Поддержка метаданных экземпляров](./media/instance-metadata-service/InstanceMetadata-support.png "Снимок экрана: открытие обращения в службу поддержки при возникновении проблем со службой метаданных экземпляра")

## <a name="next-steps"></a>Next Steps

См. также:
1. [Получение маркера доступа для виртуальной машины](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).
1. [Запланированные события](scheduled-events.md)