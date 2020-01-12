---
title: Развертывание на выделенных узлах
description: Использование выделенных узлов для достижения действительной изоляции уровня узла для рабочих нагрузок
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 619a39f4d08a4308cb0f566bc50860e9562bf9e4
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903760"
---
# <a name="deploy-on-dedicated-hosts"></a>Развертывание на выделенных узлах

"Выделенный" — это номер SKU для службы "экземпляры контейнеров Azure" (ACI), предоставляющий изолированную и выделенную среду вычислений для безопасного выполнения контейнеров. Использование выделенного номера SKU в каждой группе контейнеров имеет выделенный физический сервер в центре обработки данных Azure, обеспечивая полную изоляцию рабочей нагрузки для обеспечения безопасности и соответствия требованиям вашей организации. 

Выделенный номер SKU подходит для рабочих нагрузок контейнера, требующих изоляции рабочей нагрузки с точки зрения физического сервера.

## <a name="using-the-dedicated-sku"></a>Использование выделенного номера SKU

> [!IMPORTANT]
> Использование выделенного номера SKU доступно только в последней версии API (2019-12-01), которая находится в процессе развертывания. Укажите версию API в шаблоне развертывания. Кроме того, ограничение по умолчанию для любой подписки, использующей выделенный номер SKU, равно 0. Если вы хотите использовать этот номер SKU для развертывания рабочих контейнеров, создайте [Поддержка Azure][azure-support] .

Начиная с API версии 2019-12-01, в разделе свойств группы контейнеров шаблона развертывания имеется свойство SKU, которое требуется для развертывания ACI. В настоящее время это свойство можно использовать как часть шаблона развертывания Azure Resource Manager для ACI. Дополнительные сведения о развертывании ресурсов ACI с помощью шаблона см. в [руководстве по развертыванию группы с несколькими контейнерами с использованием шаблона диспетчер ресурсов](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

Свойство SKU может иметь одно из следующих значений:
* Standard — стандартный вариант развертывания ACI, который по-прежнему гарантирует безопасность на уровне гипервизора. 
* Выделенный — используется для изоляции уровня рабочей нагрузки с выделенными физическими узлами для группы контейнеров.

## <a name="modify-your-json-deployment-template"></a>Изменение шаблона развертывания JSON

В шаблоне развертывания, где указан ресурс группы контейнеров, убедитесь, что `"apiVersion": "2019-12-01",`. В разделе Свойства ресурса группы контейнеров задайте `"sku": "Dedicated",`.

Ниже приведен пример фрагмента для раздела Resources шаблона развертывания группы контейнеров, который использует выделенный номер SKU:

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
                "osType": "Linux",
            },
            "location": "eastus2euap",
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Развертывание группы контейнеров

Если вы создали и редактировали файл шаблона развертывания на рабочем столе, его можно передать в каталог Cloud Shell, перетащив в него файл. 

Создайте группу ресурсов с помощью команды [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Разверните шаблон с помощью команды [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

В течение нескольких секунд вы должны получить исходный ответ Azure. После завершения развертывания все данные, связанные с ним, сохраненные службой ACI, будут зашифрованы с помощью предоставленного ключа.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
