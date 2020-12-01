---
title: Служба метаданных экземпляров Azure для Windows
description: Сведения о службе метаданных экземпляров Azure и о том, как она предоставляет сведения о выполняемых в данный момент экземплярах виртуальных машин в Windows.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: cc0d47807101a3cfbb26e7ea69cc7d117d3f9b31
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435235"
---
# <a name="azure-instance-metadata-service"></a>Служба метаданных экземпляров Azure

Служба метаданных экземпляров Azure (IMDS) предоставляет сведения о текущих экземплярах виртуальных машин. Его можно использовать для управления виртуальными машинами и их настройки.
Эти сведения включают SKU, хранилище, конфигурации сети и будущие события обслуживания. Полный список доступных данных см. в разделе [API метаданных](#metadata-apis).


IMDS доступен для запуска экземпляров виртуальных машин и экземпляров масштабируемых наборов виртуальных машин. Все API-интерфейсы поддерживают виртуальные машины, созданные и управляемые с помощью [Azure Resource Manager](/rest/api/resources/). Виртуальные машины, созданные с помощью классической модели развертывания, поддерживают только конечные точки аттестации и сети. Аттестация конечной точки выполняется только в ограниченном экстенте.

IMDS — это конечная точка RESTFUL, доступная по известному, не поддерживающему маршрутизацию IP-адресу ( `169.254.169.254` ). Доступ к нему можно получить только в виртуальной машине. Обмен данными между виртуальной машиной и IMDS никогда не покидает узел.
Попросите клиентов HTTP пропускать веб-прокси в виртуальной машине при запросе IMDS и обрабатывать `169.254.169.254` их так же, как [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Безопасность

Конечная точка IMDS доступна только в работающем экземпляре виртуальной машины на IP-адресе, не поддерживающем маршрутизацию. Кроме того, любой запрос с `X-Forwarded-For` заголовком отклоняется службой.
Запросы также должны содержать `Metadata: true` заголовок, чтобы гарантировать, что фактический запрос был непосредственно предназначен и не является частью непреднамеренного перенаправления.

> [!IMPORTANT]
> IMDS не является каналом для конфиденциальных данных. Конечная точка открыта для всех процессов на виртуальной машине. Рассмотрите сведения, предоставляемые этой службой в качестве общей информации для всех приложений, выполняющихся на виртуальной машине.

## <a name="usage"></a>Использование

### <a name="access-azure-instance-metadata-service"></a>Доступ к службе метаданных экземпляров Azure

Чтобы получить доступ к IMDS, создайте виртуальную машину на основе [Azure Resource Manager](/rest/api/resources/) или [портал Azure](https://portal.azure.com)и используйте следующие примеры.
Дополнительные примеры см. в статье [примеры метаданных экземпляров Azure](https://github.com/microsoft/azureimds).

Ниже приведен пример кода для получения всех метаданных для экземпляра. Сведения о доступе к определенному источнику данных см. в разделе [API метаданных](#metadata-apis) . 

**Запрос**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance?api-version=2020-09-01 | ConvertTo-Json
```
> [!NOTE]
> `-NoProxy`Флаг доступен только в PowerShell 6 или более поздней версии. Вы можете опустить флаг, если у вас нет настройки прокси-сервера.

**Ответ**

> [!NOTE]
> Ответ представляет собой строку JSON. Чтобы выполнить печать, переведите запрос с помощью `ConvertTo-Json` командлета.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
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
        "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
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
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
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
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
            "ipv4": {
               "ipAddress": [{
                    "privateIpAddress": "10.144.133.132",
                    "publicIpAddress": ""
                }],
                "subnet": [{
                    "address": "10.144.133.128",
                    "prefix": "26"
                }]
            },
            "ipv6": {
                "ipAddress": [
                 ]
            },
            "macAddress": "0011AAFFBB22"
        }]
    }
}
```

### <a name="data-output"></a>Выходные данные

По умолчанию IMDS возвращает данные в формате JSON ( `Content-Type: application/json` ). Однако при запросе некоторые API-интерфейсы могут возвращать данные в разных форматах.
В следующей таблице перечислены другие форматы данных, которые могут поддерживаться интерфейсами API.

API | Формат данных по умолчанию | Другие форматы
--------|---------------------|--------------
/attested | json | none
/Identity | json | none
/instance | json | text
/scheduledevents | json | none

Для доступа к нестандартному формату ответа укажите запрошенный формат в качестве параметра строки запроса. Пример:

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Для конечных узлов в `/metadata/instance` `format=json` не работает. Для этих запросов необходимо `format=text` явно указать, так как по умолчанию используется формат JSON.

### <a name="version"></a>Версия

IMDS имеет версию, и указание версии API в HTTP-запросе является обязательным.

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
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> Версия 2020-10-01 может быть пока недоступна в каждом регионе.

По мере добавления новых версий можно получить доступ к более старым версиям для обеспечения совместимости, если сценарии имеют зависимости от конкретных форматов данных.

Если вы не укажете версию, появится сообщение об ошибке со списком последних поддерживаемых версий.

> [!NOTE]
> Ответ представляет собой строку JSON. В следующем примере показано состояние ошибки, если версия не указана. Ответ довольно удобочитаем.

**Запрос**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance
```

**Ответ**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

## <a name="metadata-apis"></a>API метаданных

IMDS содержит несколько интерфейсов API, представляющих различные источники данных.

API | Описание | Представленная версия
----|-------------|-----------------------
/attested | Просмотр [аттестацических данных](#attested-data) | 2018-10-01
/Identity | См. раздел [Получение маркера доступа](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | См. раздел [API экземпляра](#instance-api) | 2017-04-02
/scheduledevents | Просмотреть [запланированные события](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>API экземпляра

API экземпляра предоставляет важные метаданные для экземпляров виртуальных машин, включая виртуальную машину, сеть и хранилище. Для доступа к следующим категориям можно использовать следующие категории `instance/compute` :

Данные | Описание | Представленная версия
-----|-------------|-----------------------
azEnvironment | Среда Azure, в которой выполняется виртуальная машина. | 2018-10-01
customData | Эта функция сейчас отключена. | 2019-02-01
ишосткомпатибилитилайервм | Определяет, выполняется ли виртуальная машина на уровне совместимости узла. | 2020-06-01
licenseType | Тип лицензии для [преимущество гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Обратите внимание, что эта возможность имеется только для виртуальных машин с поддержкой АХБ. | 2020-09-01
location | Регион Azure, в котором выполняется виртуальная машина. | 2017-04-02
name | Имя виртуальной машины. | 2017-04-02
offer | Предоставляют сведения об образе виртуальной машины. Эта проблема имеется только для образов, развернутых из коллекции образов Azure. | 2017-04-02
osProfile.adminUsername | Указывает имя учетной записи администратора. | 2020-07-15
osProfile. computerName | Указывает имя компьютера. | 2020-07-15
osProfile. Дисаблепассвордаусентикатион | Указывает, отключена ли проверка пароля. Обратите внимание, что эта проблема имеется только для виртуальных машин Linux. | 2020-10-01
osType | Linux или Windows. | 2017-04-02
placementGroupId | [Группа размещения](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) масштабируемого набора виртуальных машин. | 2017-08-01
План | [План](/rest/api/compute/virtualmachines/createorupdate#plan) , содержащий имя, продукт и издателя для виртуальной машины, если это образ Azure Marketplace. | 2018-04-02
platformUpdateDomain |  [Обновление домена](../manage-availability.md) , в котором выполняется виртуальная машина. | 2017-04-02
platformFaultDomain | [Домен сбоя](../manage-availability.md) , в котором выполняется виртуальная машина. | 2017-04-02
поставщик | Поставщик виртуальной машины. | 2018-10-01
publicKeys | [Коллекция открытых ключей](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) , назначенных виртуальной машине и путям. | 2018-04-02
publisher | Издатель образа виртуальной машины. | 2017-04-02
имя_группы_ресурсов | [Группа ресурсов](../../azure-resource-manager/management/overview.md) для виртуальной машины. | 2017-08-01
resourceId | Полный [идентификатор ресурса](/rest/api/resources/resources/getbyid) . | 2019-03-11
sku | Конкретный номер SKU для образа виртуальной машины. | 2017-04-02
Секуритипрофиле. secureBootEnabled | Определяет, включена ли безопасная Загрузка UEFI на виртуальной машине. | 2020-06-01
Секуритипрофиле. Виртуалтпменаблед | Определяет, включен ли на виртуальной машине виртуальный доверенный платформенный модуль (TPM) (TPM). | 2020-06-01
storageProfile | См. раздел [профиль хранилища](#storage-metadata). | 2019-06-01
subscriptionId | Подписка Azure для виртуальной машины. | 2017-08-01
tags | [Теги](../../azure-resource-manager/management/tag-resources.md) для виртуальной машины.  | 2017-08-01
tagsList | Теги форматируются в виде массива JSON для упрощения программного анализа.  | 2019-06-04
version | Версия образа виртуальной машины. | 2017-04-02
vmId | [Уникальный идентификатор](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) виртуальной машины. | 2017-04-02
vmScaleSetName | [Имя масштабируемого набора виртуальных машин](../../virtual-machine-scale-sets/overview.md) для масштабируемого набора виртуальных машин. | 2017-12-01
vmSize | См. [Размер виртуальной машины](../sizes.md). | 2017-04-02
зона | [Зона доступности](../../availability-zones/az-overview.md) виртуальной машины. | 2017-12-01

### <a name="sample-1-track-a-vm-running-on-azure"></a>Пример 1. мониторинг виртуальной машины, работающей в Azure

В качестве поставщика услуг может потребоваться отслеживание количества виртуальных машин, на которых выполняется программное обеспечение, или наличие агентов, которым необходимо отслеживание уникальности виртуальной машины. Чтобы получить уникальный идентификатор для виртуальной машины, используйте `vmId` поле из IMDS.

**Запрос**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Ответ**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>Пример 2. размещение различных реплик данных

В некоторых сценариях размещение разных реплик имеет первостепенное значение. Например, при [размещении реплики HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) или размещении контейнера с помощью [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) может потребоваться информация о том, `platformFaultDomain` `platformUpdateDomain` на котором запущена виртуальная машина.
Кроме того, для принятия этих решений можно применить [Зоны доступности](../../availability-zones/az-overview.md) для экземпляров.
Эти данные можно запросить непосредственно через IMDS.

**Запрос**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Ответ**

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>Пример 3. получение дополнительных сведений о виртуальной машине во время обращения в службу поддержки

Как поставщик услуг, вы можете позвонить в службу поддержки, где вы хотите получить дополнительные сведения о виртуальной машине. В этом случае можно запрашивать у клиента общий доступ к метаданным вычислений.

**Запрос**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01
```

**Ответ**

> [!NOTE]
> Ответ представляет собой строку JSON. Следующие примеры ответов представлены в удобном для чтения формате.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
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
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
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
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
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
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>Пример 4. получение среды Azure, в которой запущена виртуальная машина

В Azure есть различные облака независимых, например [Azure для государственных организаций](https://azure.microsoft.com/overview/clouds/government/). Иногда требуется среда Azure для принятия некоторых решений во время выполнения. В следующем примере показано, как добиться такого поведения.

**Запрос**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Ответ**

```text
AzurePublicCloud
```

Облако и значения среды Azure перечислены здесь.

 Cloud   | Среда Azure
---------|-----------------
[Все общедоступные глобальные регионы Azure](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure для государственных организаций](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud;
[Azure для Германии](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Метаданные сети 

Метаданные сети являются частью API экземпляра. В конечной точке доступны следующие категории сети `instance/network` .

Данные | Описание | Представленная версия
-----|-------------|-----------------------
ipv4/privateIpAddress | Локальный IPv4-адрес виртуальной машины. | 2017-04-02
ipv4/publicIpAddress | Общедоступный IPv4-адрес виртуальной машины. | 2017-04-02
subnet/address | Адрес подсети виртуальной машины. | 2017-04-02
subnet/prefix | Префикс подсети. Пример: 24 | 2017-04-02
ipv6/ipAddress | Локальный IPv6-адрес виртуальной машины. | 2017-04-02
macAddress | Mac-адрес виртуальной машины. | 2017-04-02

> [!NOTE]
> Все ответы API — это строки в формате JSON. Все следующие примеры ответов в целях удобочитаемости представлены в структурированном виде.

#### <a name="sample-1-retrieve-network-information"></a>Пример 1. Получение сведений о сети

**Запрос**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/network?api-version=2017-08-01
```

**Ответ**

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

#### <a name="sample-2-retrieve-public-ip-address"></a>Пример 2. получение общедоступного IP-адреса

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Метаданные хранилища

Метаданные хранилища являются частью API экземпляра в `instance/compute/storageProfile` конечной точке.
Он предоставляет сведения о дисках хранилища, связанных с виртуальной машиной. 

Профиль хранилища виртуальной машины делится на три категории: ссылка на образ, диск операционной системы и диски данных.

Объект ссылки на изображение содержит следующие сведения об образе операционной системы.

Данные    | Описание
--------|-----------------
идентификатор      | Идентификатор ресурса
offer   | Предложение платформы или образа
publisher | Издатель образа.
sku     | Номер SKU образа.
version | Версия платформы или образа

Объект диска операционной системы содержит следующие сведения о диске операционной системы, используемом виртуальной машиной.

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
osType  | Тип операционной системы, включаемой в диск
vhd     | Виртуальный жесткий диск
writeAcceleratorEnabled | Включение или выключение `writeAccelerator` на диске

Массив дисков данных содержит список дисков данных, подключенных к виртуальной машине. Каждый объект диска данных содержит следующие сведения.

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
writeAcceleratorEnabled | Включение или выключение `writeAccelerator` на диске

В следующем примере показано, как запросить сведения о хранилище виртуальной машины.

**Запрос**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01
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

Теги виртуальных машин включены в API экземпляра в `instance/compute/tags` конечной точке.
К виртуальной машине Azure могут быть применены теги для логической организации их в таксономию. Вы можете получить теги, назначенные виртуальной машине, с помощью следующего запроса.

**Запрос**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Ответ**

```text
Department:IT;Environment:Test;Role:WebRole
```

Поле `tags` представляет собой строку с тегами, разделенными точкой с запятой. Эти выходные данные могут быть проблемой, если в самих тегах используются точки с запятой. Если средство синтаксического анализа написано для программного извлечения тегов, следует полагаться на `tagsList` поле. `tagsList`Поле является массивом JSON без разделителей, поэтому его проще проанализировать.

**Запрос**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04
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

## <a name="attested-data"></a>Аттестация данных

IMDS помогает предоставлять гарантию того, что предоставленные данные поступают из Azure. Корпорация Майкрософт подписывает часть этих сведений, чтобы вы могли убедиться, что образ в Azure Marketplace — это тот, который вы используете в Azure.

### <a name="sample-1-get-attested-data"></a>Пример 1. получение аттестацических данных

> [!NOTE]
> Все ответы API — это строки в формате JSON. Все следующие примеры ответов представлены в удобном для чтения формате.

**Запрос**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

> [!NOTE]
> Из-за механизма кэширования IMDS может возвращаться ранее кэшированное `nonce` значение.

`Api-version` является обязательным полем. Поддерживаемые версии API см. в разделе [Использование](#usage).
`Nonce` — Необязательная строка из 10 цифр. Если она не указана, IMDS возвращает текущую скоординированную метку времени в формате UTC.

**Ответ**

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

BLOB-объект сигнатуры — это версия документа, подписанная [PKCS7](https://aka.ms/pkcs7). Он содержит сертификат, используемый для подписывания, а также некоторые сведения, относящиеся к виртуальной машине. 

Для виртуальных машин, созданных с помощью Azure Resource Manager, сюда входят,,,, `vmId` `sku` `nonce` `subscriptionId` `timeStamp` для создания и окончания срока действия документа, а также сведения о плане образа. Сведения о плане заполняются только для образов из Azure Marketplace. 

Для виртуальных машин, созданных с помощью классической модели развертывания, `vmId` гарантируется только заполнение. Вы можете извлечь сертификат из ответа и использовать его для подтверждения того, что ответ действителен и поступает из Azure.

Документ содержит следующие поля:

Данные | Описание | Представленная версия
-----|-------------|-----------------------
licenseType | Тип лицензии для [преимущество гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Обратите внимание, что эта возможность имеется только для виртуальных машин с поддержкой АХБ. | 2020-09-01
nonce | Строка, которую можно дополнительно указать с помощью запроса. Если `nonce` значение не указано, используется текущая отметка времени координированного скоординированного периода. | 2018-10-01
План | [План образа Azure Marketplace](/rest/api/compute/virtualmachines/createorupdate#plan). Содержит идентификатор плана (имя), образ продукта или предложение (продукт) и идентификатор издателя (издатель). | 2018-10-01
timestamp/createdOn | Отметка времени в формате UTC для момента создания подписанного документа. | 2018-20-01
timestamp/expiresOn | Отметка времени в формате UTC для момента истечения срока действия подписанного документа. | 2018-10-01
vmId |  [Уникальный идентификатор](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) виртуальной машины. | 2018-10-01
subscriptionId | Подписка Azure для виртуальной машины. | 2019-04-30
sku | Конкретный номер SKU для образа виртуальной машины. | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>Пример 2. Проверка того, что виртуальная машина запущена в Azure

Поставщики в Azure Marketplace хотят убедиться, что их программное обеспечение лицензировано для запуска только в Azure. Если кто-то копирует виртуальный жесткий диск в локальную среду, поставщик должен иметь возможность обнаружить это. С помощью IMDS эти поставщики могут получать подписанные данные, гарантирующие ответ только из Azure.

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Убедитесь, что подпись относится к Microsoft Azure и проверьте цепочку сертификатов на наличие ошибок.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

> [!NOTE]
> Из-за механизма кэширования IMDS может возвращаться ранее кэшированное `nonce` значение.

Объект `nonce` в подписанном документе можно сравнить, если `nonce` в первоначальном запросе был указан параметр.

> [!NOTE]
> Сертификат для общедоступного облака и каждого независимых облака будут отличаться.

Cloud | Сертификат
------|------------
[Все общедоступные глобальные регионы Azure](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure для государственных организаций](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure для Германии](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> Сертификаты могут не иметь точного соответствия `metadata.azure.com` для общедоступного облака. По этой причине проверка сертификата должна допускать общее имя из любого `.metadata.azure.com` поддомена.

В случаях, когда промежуточный сертификат не может быть загружен из-за сетевых ограничений во время проверки, можно закрепить промежуточный сертификат. Обратите внимание, что Azure выполняет накат сертификатов, что является стандартной практикой PKI. Если происходит смена, необходимо обновить закрепленные сертификаты. При каждом запланированном изменении промежуточного сертификата блог Azure обновляется, и клиенты Azure получают уведомления. 

Промежуточные сертификаты можно найти в [репозитории PKI](https://www.microsoft.com/pki/mscorp/cps/default.htm). Для разных регионов могут использоваться различные промежуточные сертификаты.

> [!NOTE]
> Промежуточный сертификат для Azure Китая (21Vianet) — от глобального корневого ЦС DigiCert, а не Baltimore.
Если вы закрепляете промежуточные сертификаты для Azure для Китая в рамках изменения центра корневых цепочек, необходимо обновить промежуточные сертификаты.

## <a name="failover-clustering-in-windows-server"></a>Отказоустойчивая кластеризация в Windows Server

При запросе IMDS с помощью отказоустойчивой кластеризации иногда необходимо добавить маршрут в таблицу маршрутизации. Это делается так.

1. Откройте окно командной строки с правами администратора.

1. Выполните следующую команду и запишите адрес интерфейса для назначения сети ( `0.0.0.0` ) в таблице маршрутов IPv4.

```bat
route print
```

> [!NOTE]
> Ниже приведен пример выходных данных из виртуальной машины Windows Server с включенным отказоустойчивым кластером. Для простоты выходные данные содержат только таблицу маршрутов IPv4.

```text
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
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

Выполните следующую команду и используйте адрес интерфейса для назначения сети ( `0.0.0.0` ), `10.0.1.10` в этом примере — ().

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="managed-identity"></a>Управляемое удостоверение

Управляемое удостоверение, назначенное системой, может быть включено на виртуальной машине. Можно также назначить виртуальной машине одно или несколько управляемых удостоверений, назначенных пользователем.
Затем можно запросить маркеры для управляемых удостоверений из IMDS. Используйте эти токены для проверки подлинности в других службах Azure, например Azure Key Vault.

Подробные инструкции по включению этой функции см. в разделе [Получение маркера доступа](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events"></a>Запланированные события
Состояние запланированных событий можно получить с помощью IMDS. Затем пользователь может указать набор действий, выполняемых при выполнении этих событий. Дополнительные сведения см. в разделе [запланированные события](scheduled-events.md). 

## <a name="regional-availability"></a>Доступность по регионам

Служба общедоступна во всех облаках Azure.

## <a name="sample-code-in-different-languages"></a>Пример кода на разных языках

В следующей таблице перечислены примеры вызова IMDS с использованием разных языков в виртуальной машине.

Язык      | Пример
--------------|----------------
C++ (Windows) | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Node.js        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Visual Basic  | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb

## <a name="errors-and-debugging"></a>Ошибки и отладка

Если элемент данных не найден или неправильно сформированный запрос, IMDS возвращает стандартные ошибки HTTP. Пример:

Код состояния HTTP | Причина
-----------------|-------
200 ОК |
400 — недопустимый запрос | Отсутствует `Metadata: true` заголовок или отсутствующий параметр `format=json` при запросе конечного узла.
404 — не найдено  | Запрошенный элемент не существует.
405 — недопустимый метод | `GET`Поддерживаются только запросы.
410 — потеряно | Повторите попытку через некоторое время в течение не более 70 секунд.
429 — слишком много запросов | Сейчас API поддерживает не более 5 запросов в секунду.
500 — ошибка службы     | Повторите попытку через некоторое время.

### <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

**Я получаю сообщение об ошибке `400 Bad Request, Required metadata header not specified` . Что это означает?**

IMDS требует передачи заголовка `Metadata: true` в запрос. Передача этого заголовка в вызове RESTFUL обеспечивает доступ к IMDS.

**Почему я не получаю сведения о вычислениях для виртуальной машины?**

В настоящее время IMDS поддерживает только экземпляры, созданные с помощью Azure Resource Manager.

**Моя виртуальная машина создана с помощью Azure Resource Manager некоторое время назад. Почему я не вижу сведений о метаданных вычислений?**

Если вы создали виртуальную машину после сентября 2016, добавьте [тег](../../azure-resource-manager/management/tag-resources.md) , чтобы начать просмотр метаданных вычислений. Если вы создали виртуальную машину до сентября 2016, добавьте или удалите расширения или диски данных для экземпляра виртуальной машины, чтобы обновить метаданные.

**Почему я не вижу все данные, заполненные для новой версии?**

Если вы создали виртуальную машину после сентября 2016, добавьте [тег](../../azure-resource-manager/management/tag-resources.md) , чтобы начать просмотр метаданных вычислений. Если вы создали виртуальную машину до сентября 2016, добавьте или удалите расширения или диски данных для экземпляра виртуальной машины, чтобы обновить метаданные.

**Почему я получаю сообщение об ошибке `500 Internal Server Error` или `410 Resource Gone` ?**

Повторите запрос. Дополнительные сведения см. в разделе [обработка временных сбоев](/azure/architecture/best-practices/transient-faults). Если проблема будет повторяться, создайте ошибку поддержки в портал Azure виртуальной машины.

**Будет ли это работать для экземпляров масштабируемого набора виртуальных машин?**

Да, IMDS доступен для экземпляров масштабируемых наборов виртуальных машин.

**Я обновил теги в масштабируемых наборах виртуальных машин, но они не отображаются в экземплярах (в отличие от виртуальных машин с одним экземпляром). Я делаю что-то не так?**

В настоящее время теги для масштабируемых наборов виртуальных машин отображаются только для виртуальной машины при перезагрузке, повторном образе или изменении диска на экземпляр.

**Почему время ожидания запроса истекло для моего вызова службы?**

Вызовы метаданных должны выполняться с основного IP-адреса, назначенного основной сетевой карте виртуальной машины. Кроме того, если вы изменили маршруты, в локальной таблице маршрутизации виртуальной машины должен быть маршрут для адреса 169.254.169.254/32.
   * <details>
        <summary>Проверка таблицы маршрутизации</summary>

        1. Выведите дамп локальной таблицы маршрутизации и найдите запись IMDS. Пример:
            ```console
            > route print
            IPv4 Route Table
            ===========================================================================
            Active Routes:
            Network Destination        Netmask          Gateway       Interface  Metric
                      0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                    127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                    127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
              127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
                168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
              169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
            ... (continues) ...
            ```
        1. Убедитесь, что маршрут существует для `169.254.169.254` , и обратите внимание на соответствующий сетевой интерфейс (например, `172.16.69.7` ).
        1. Выведите дамп конфигурации интерфейса и найдите интерфейс, соответствующий тому, на который ссылается таблица маршрутизации, указывая MAC-адрес (физический).
            ```console
            > ipconfig /all
            ... (continues) ...
            Ethernet adapter Ethernet:

               Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
               Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
               Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
               DHCP Enabled. . . . . . . . . . . : Yes
               Autoconfiguration Enabled . . . . : Yes
               Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
               IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
               Subnet Mask . . . . . . . . . . . : 255.255.255.0
            ... (continues) ...
            ```
        1. Убедитесь, что интерфейс соответствует основному сетевому адаптеру виртуальной машины и основному IP-адресу. Основной сетевой адаптер и IP-адрес можно найти, просмотрев конфигурацию сети в портал Azure или выполнив поиск в Azure CLI. Обратите внимание на общедоступные и частные IP-адреса (и MAC-адрес, если вы используете интерфейс командной строки). Вот пример PowerShell CLI:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: wintest767 True 00-0D-3A-E5-1C-C0
            ```
        1. Если они не совпадают, обновите таблицу маршрутизации, чтобы обеспечить целевую основную сетевую карту и IP-адрес.
    </details>

## <a name="support"></a>Поддержка

Если вы не сможете получить ответ на метаданные после нескольких попыток, можно создать ошибку поддержки в портал Azure.
В качестве **типа проблемы** выберите **Управление**. В качестве **категории** выберите **Служба метаданных экземпляра**.

![Снимок экрана: поддержка службы метаданных экземпляров](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Дальнейшие действия

[Получение маркера доступа для виртуальной машины](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Запланированные события](scheduled-events.md)
