---
title: Настройка частной конечной точки (Предварительная версия)
titleSuffix: Azure Machine Learning
description: Используйте частную ссылку Azure для безопасного доступа к рабочей области Машинное обучение Azure из виртуальной сети.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/28/2020
ms.openlocfilehash: 9ce139131e2c6cbfd73f9160b986d9886ae4844b
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181958"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Настройка частной ссылки Azure для Машинное обучение Azure рабочей области (Предварительная версия)

В этом документе вы узнаете, как использовать частную связь Azure с рабочей областью Машинное обучение Azure. 

> [!IMPORTANT]
> Использование частной связи Azure с Машинное обучение Azure рабочей областью в настоящее время находится в общедоступной предварительной версии. Эта функция доступна только в восточной части **США**, в **Южной Центральной части США** и в **западной части США 2** . Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Частная ссылка Azure позволяет подключаться к рабочей области с помощью частной конечной точки. Частная конечная точка — это набор частных IP-адресов в виртуальной сети. Затем можно ограничить доступ к рабочей области, чтобы она выполнялась только по частным IP-адресам. Частная ссылка помогает снизить риск утечка данных. Дополнительные сведения о частных конечных точках см. в статье [Приватный канал Azure](/azure/private-link/private-link-overview).

> [!IMPORTANT]
> Частная ссылка Azure не влияет на плоскость управления Azure (операции управления), такие как удаление рабочей области или управление ресурсами вычислений. Например, создание, обновление или удаление целевого объекта вычислений. Эти операции выполняются через общедоступный Интернет в нормальном режиме.
>
> При использовании Mozilla Firefox могут возникнуть проблемы при попытке доступа к закрытой конечной точке рабочей области. Эта проблема может быть связана с DNS по протоколу HTTPS в Mozilla. В качестве обходного пути рекомендуется использовать Microsoft ребро Google Chrome.

> [!TIP]
> Машинное обучение Azure вычислительный экземпляр можно использовать с рабочей областью и частной конечной точкой. В настоящее время эта возможность доступна в общедоступной предварительной версии в **восточной части США**, в **Южной Центральной** части США и в **западной части США 2** .

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Создание рабочей области, использующей закрытую конечную точку

> [!IMPORTANT]
> Сейчас мы поддерживаем только включение частной конечной точки при создании новой рабочей области Машинное обучение Azure.

Шаблон [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) можно использовать для создания рабочей области с частной конечной точкой.

Сведения об использовании этого шаблона, включая частные конечные точки, см. в разделе [Использование шаблона Azure Resource Manager для создания рабочей области для машинное обучение Azure](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Использование рабочей области в частной конечной точке

Так как взаимодействие с рабочей областью разрешено только из виртуальной сети, все среды разработки, использующие эту рабочую область, должны быть членами виртуальной сети. Например, виртуальная машина в виртуальной сети.

> [!IMPORTANT]
> Чтобы избежать временного сбоя подключения, корпорация Майкрософт рекомендует сбрасывать кэш DNS на компьютерах, подключающихся к рабочей области, после включения частной связи. 

Сведения о виртуальных машинах Azure см. в [документации по виртуальным машинам](/azure/virtual-machines/).


## <a name="using-azure-storage"></a>Использование службы хранилища Azure

Чтобы защитить учетную запись хранения Azure, используемую рабочей областью, вставьте ее в виртуальную сеть.

Сведения о размещении учетной записи хранения в виртуальной сети см. в статье [Использование учетной записи хранения для рабочей области](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace).

> [!WARNING]
> Машинное обучение Azure не поддерживает использование учетной записи хранения Azure, для которой включена частная ссылка.

## <a name="using-azure-key-vault"></a>Использование Azure Key Vault

Чтобы защитить Azure Key Vault, используемые рабочей областью, можно либо разместить ее в виртуальной сети, либо включить для нее закрытую ссылку.

Сведения о размещении хранилища ключей в виртуальной сети см. в статье [использование экземпляра хранилища ключей с рабочей областью](how-to-enable-virtual-network.md#key-vault-instance).

Сведения о включении закрытой ссылки для хранилища ключей см. в статье [интеграция Key Vault с помощью частного канала Azure](/azure/key-vault/private-link-service).

## <a name="using-azure-kubernetes-services"></a>Использование Azure Kubernetes Services

Чтобы защитить службы Azure Kubernetes, используемые рабочей областью, вставьте ее в виртуальную сеть. Дополнительные сведения см. в статье [Использование Azure Kubernetes Services с рабочей областью](how-to-enable-virtual-network.md#aksvnet).

Машинное обучение Azure теперь поддерживает использование службы Azure Kubernetes, для которой включена частная ссылка.
Чтобы создать частный кластер AKS, следуйте указаниям [здесь](https://docs.microsoft.com/azure/aks/private-clusters)

## <a name="azure-container-registry"></a>Реестр контейнеров Azure

Сведения о защите реестра контейнеров Azure в виртуальной сети см. в статье [использование реестра контейнеров Azure](how-to-enable-virtual-network.md#azure-container-registry).

> [!IMPORTANT]
> Если используется частная ссылка для рабочей области Машинное обучение Azure и размещен реестр контейнеров Azure для рабочей области в виртуальной сети, необходимо также применить следующий шаблон Azure Resource Manager. Этот шаблон позволяет рабочей области взаимодействовать с записью контроля доступа через частную ссылку.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о защите рабочей области Машинное обучение Azure см. в статье [Корпоративная безопасность](concept-enterprise-security.md) .