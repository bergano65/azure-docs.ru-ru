---
title: Развертывание на выделении узла
description: Используйте специальный хост для достижения истинной изоляции уровня хоста для рабочих нагрузок Azure Container Instances
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: a614d6b5d0cf5c6c1df5ffcb90e56960d6b8a2a9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025039"
---
# <a name="deploy-on-dedicated-hosts"></a>Развертывание на выделенных узлах

"Посвященный" - это sku, обеспечивающий изолированную и выделенную среду вычислений для надежно работающих контейнеров. Использование выделенного sku приводит к тому, что каждая группа контейнеров имеет выделенный физический сервер в центре обработки данных Azure, обеспечивая полную изоляцию рабочей нагрузки, чтобы соответствовать требованиям безопасности и соответствия требованиям организации. 

Выделенный sku подходит для рабочих нагрузок контейнеров, которые требуют изоляции рабочей нагрузки от физического сервера.

## <a name="prerequisites"></a>Предварительные требования

* Предел по умолчанию для любой подписки на использование выделенного sku составляет 0. Если вы хотите использовать этот sku для развертывания производственных контейнеров, создайте [запрос службы поддержки Azure][azure-support] для увеличения лимита.

## <a name="use-the-dedicated-sku"></a>Используйте выделенный sku

> [!IMPORTANT]
> Использование выделенного sku доступно только в последней версии API (2019-12-01), которая в настоящее время развертывается. Укажите эту версию API в шаблоне развертывания.
>

Начиная с версии API 2019-12-01, есть свойство под разделом `sku` свойств контейнерной группы шаблона развертывания, которое требуется для развертывания ACI. В настоящее время это свойство можно использовать как часть шаблона развертывания ресурсов Azure Manager для ACI. Подробнее о развертывании ресурсов ACI с шаблоном в [учебном варианте: Развертывание многоконтейнерной группы с помощью шаблона «Менеджер ресурсов».](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group) 

Свойство `sku` может иметь одно из следующих значений:
* `Standard`- стандартный выбор развертывания ACI, который по-прежнему гарантирует безопасность на уровне гипервизора 
* `Dedicated`- используется для изоляции уровня рабочей нагрузки с выделенными физическими узлами для контейнерной группы

## <a name="modify-your-json-deployment-template"></a>Изменение шаблона развертывания JSON

В шаблоне развертывания измените или добавьте следующие свойства:
* Под, `resources` `apiVersion` установленным на `2019-12-01`.
* Под свойствами группы `sku` контейнеров `Dedicated`добавьте свойство со значением.

Вот пример фрагмента для раздела ресурсов шаблона развертывания контейнерной группы, в который используется выделенный sku:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

Ниже приводится полный шаблон, который развертывает образец контейнерной группы под управлением одного экземпляра контейнера:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Развертывание контейнерной группы

Если вы создали и отредактировали файл шаблона развертывания на рабочем столе, вы можете загрузить его в каталог Cloud Shell, перетащив файл в него. 

Создайте группу ресурсов с помощью команды [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Разверните шаблон с помощью команды [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

В течение нескольких секунд вы должны получить исходный ответ Azure. Успешное развертывание происходит на выделенном усте.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
