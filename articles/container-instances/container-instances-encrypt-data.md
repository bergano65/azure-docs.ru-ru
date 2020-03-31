---
title: Шифрование данных развертывания
description: Узнайте о шифровании данных, сохраняемых для ресурсов экземпляра контейнера, и о том, как шифровать данные с помощью ключа, управляемого клиентом
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: ad232c5d9df9f6bfae3a79dbd72e2c68143be949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080366"
---
# <a name="encrypt-deployment-data"></a>Шифрование данных развертывания

При запуске ресурсов Azure Container Instances (ACI) в облаке служба ACI собирает и сохраняет данные, связанные с контейнерами. ACI автоматически шифрует эти данные, когда они сохраняются в облаке. Это шифрование защищает ваши данные, чтобы помочь выполнить обязательства организации по обеспечению безопасности и соответствию требованиям. ACI также предоставляет возможность шифровать эти данные с помощью собственного ключа, что дает вам больший контроль над данными, связанными с развертыванием ACI.

## <a name="about-aci-data-encryption"></a>О шифровании данных ACI 

Данные в ACI шифруются и расшифровываются с помощью 256-битного шифрования AES. Он включен для всех развертываний ACI, и вам не нужно изменять развертывание или контейнеры, чтобы воспользоваться этим шифрованием. Это включает метаданные о развертывании, переменные среды, ключи, передаваемые в контейнеры, и журналы сохраняются после того, как контейнеры остановлены, так что вы все еще можете их видеть. Шифрование не влияет на производительность группы контейнеров, и нет никаких дополнительных затрат на шифрование.

## <a name="encryption-key-management"></a>Управление ключами шифрования

Вы можете положиться на управляемые корпорацией Майкрософт ключи для шифрования данных контейнера, или вы можете управлять шифрованием с вашими собственными ключами. В следующей таблице сравниваются следующие варианты: 

|    |    Ключи, управляемые корпорацией Майкрософт     |     Ключи, управляемые клиентом     |
|----|----|----|
|    Операции шифрования/расшифровки    |    Azure    |    Azure    |
|    Хранение ключей    |    Ключевой магазин Майкрософт    |    Azure Key Vault    |
|    Ключевая ответственность вращения    |    Microsoft    |    Customer    |
|    Доступ к ключам    |    Только microsoft    |    Корпорация Майкрософт, Заказчик    |

Остальная часть документа охватывает шаги, необходимые для шифрования данных развертывания ACI с помощью ключа (ключ, управляемый клиентом). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Шифрование данных с помощью ключа, управляемого клиентом

### <a name="create-service-principal-for-aci"></a>Создание директора службы для ACI

Первым шагом является обеспечение того, чтобы [у арендатора Azure](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) был назначен директор службы обслуживания для предоставления разрешений службе Azure Container Instances. 

> [!IMPORTANT]
> Для успешного выполнения следующей команды и создания основного обслуживания подтвердите наличие у вас разрешений на создание в арендаторе принципов обслуживания.
>

Следующая команда CLI начнюет ACI SP в вашей среде Azure:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

Вывод из выполнения этой команды должен отображать основной службы службы, настроенный с помощью "displayName": "Служба экземпляров контейнеров Azure".

В случае, если вы не можете успешно создать основной сервис:
* подтвердить, что у вас есть разрешения на это в вашем арендаторе
* проверьте, существует ли у арендатора уже принцип службы для развертывания в ACI. Вы можете сделать `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` это, запустив и использовать, что основной службы вместо

### <a name="create-a-key-vault-resource"></a>Создание ресурса хранилища ключей

Создайте Хранилище ключей Azure с помощью [портала Azure,](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)или [PowerShell.](https://docs.microsoft.com/azure/key-vault/quick-create-powershell) 

Для свойств хранилища ключей используйте следующие рекомендации: 
* Имя — укажите имя ресурса. 
* Подписка — выберите подписку.
* В группе ресурсов либо выберите существующую группу ресурсов, либо создайте новое и введите имя группы ресурсов.
* Выберите расположение в раскрывающемся меню "Расположение".
* Другие параметры можно оставить по умолчанию или выбрать на основе дополнительных требований.

> [!IMPORTANT]
> При использовании управляемых клиентом ключей для шифрования шаблона развертывания ACI рекомендуется установить следующие два свойства на хранилище ключей: Soft Delete и Do Not Purge. Эти свойства не включены по умолчанию, но могут быть включены с помощью PowerShell или Azure CLI в новом или существующем хранилище ключей.

### <a name="generate-a-new-key"></a>Создание нового ключа 

Как только хранилище ключей создано, перейдите на ресурс на портале Azure. В левом меню навигации лезвия ресурса, под настройками, щелкните **клавиши**. На представлении для "Ключи", нажмите "Generate / Импорт", чтобы создать новый ключ. Используйте любое уникальное имя для этого ключа, и любые другие предпочтения, основанные на ваших требованиях. 

![Создание нового ключа](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Установка политики доступа

Создайте новую политику доступа, позволяющую службе ACI получить доступ к вашему ключу.

* После того, как ключ был сгенерирован, вернитесь в лезвие ресурсов хранилища ключей, под настройками, нажмите **Политики доступа.**
* На странице "Политика доступа" для хранилища ключей нажмите **"Дополнительная политика доступа"** нажмите "Дополнительная политика доступа".
* Установите *ключевые разрешения,* чтобы включить **Получить** и **развернуть ключевые** ![настройки ключей](./media/container-instances-encrypt-data/set-key-permissions.png)
* Для *выбора основного*принципата выберите **службу инстанций azure Container Instance Service**
* Нажмите **Добавить** в нижней части 

Теперь политика доступа должна отображаться в политиках доступа в хранилище ключей.

![Новая политика доступа](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Изменение шаблона развертывания JSON

> [!IMPORTANT]
> Шифрование данных о развертывании с помощью ключа, управляемого клиентом, доступно в последней версии API (2019-12-01), которая в настоящее время развертывается. Укажите эту версию API в шаблоне развертывания. Если у вас возникли проблемы с этим, пожалуйста, обратитесь в службу поддержки Azure.

После настройки ключа ключа хранилища и политики доступа добавьте следующие свойства в шаблон развертывания ACI. Подробнее о развертывании ресурсов ACI с шаблоном в [учебном варианте: Развертывание многоконтейнерной группы с помощью шаблона «Менеджер ресурсов».](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group) 
* Под, `resources` `apiVersion` установленным на `2019-12-01`.
* В разделе свойств контейнерной группы шаблона развертывания добавьте `encryptionProperties`значение, содержащее следующие значения:
  * `vaultBaseUrl`: Имя DNS хранилища ключей можно найти на бипасти обзора ресурса хранилища ключей в Portal
  * `keyName`: имя ключа, созданного ранее
  * `keyVersion`: текущая версия ключа. Это можно найти, нажав на сам ключ (в разделе "Ключи" в разделе Настройки вашего ресурса хранилища ключей)
* Под свойствами группы `sku` контейнеров `Standard`добавьте свойство со значением. Свойство `sku` требуется в версии API 2019-12-01.

Следующий фрагмент шаблона показывает эти дополнительные свойства для шифрования данных развертывания:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

Ниже приводится полный шаблон, адаптированный из шаблона в [tutorial: Развертывание многоконтейнерной группы с помощью шаблона менеджер ресурсов.](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group) 

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
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Развертывание ресурсов

Если вы создали и отредактировали файл шаблона на рабочем столе, вы можете загрузить его в каталог Cloud Shell, перетащив файл в него. 

Создайте группу ресурсов с помощью команды [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Разверните шаблон с помощью команды [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

В течение нескольких секунд вы должны получить исходный ответ Azure. Как только развертывание завершится, все данные, связанные с ним, будут зашифрованы службой ACI с помощью предоставленного вами ключа.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
