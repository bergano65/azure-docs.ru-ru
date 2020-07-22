---
title: Регистрация подключения к предварительной версии службы пиринга с помощью Azure CLI
description: Узнайте, как зарегистрировать подключение службы пиринга с помощью Azure CLI
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: fbb9a6237aafe1c1b7c94d9dd23ad4c3665c0c73
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871464"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Регистрация подключения к службе пиринга с помощью Azure CLI

Служба пиринга Azure — это сетевая служба, расширяющая возможности клиентов по подключению к облачным службам Майкрософт, таким как Office 365, Dynamics 365, SaaS (программное обеспечение как услуга), Azure и любым службам корпорации Майкрософт, которые работают через общедоступный Интернет. В этой статье вы узнаете, как зарегистрировать подключение службы пиринга с помощью Azure CLI.

Если у вас еще нет подписки Azure, [создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) прямо сейчас.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для работы с этой статьей требуется Azure CLI версии 2.0.28 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Предварительные требования 

Необходимо следующее:

### <a name="azure-account"></a>Учетная запись Azure

Вам потребуется действительная и активная учетная запись Microsoft Azure. Эта учетная запись необходима для настройки подключения Службы пиринга. Ресурс Службы пиринга размещается в подписках Azure.

### <a name="connectivity-provider"></a>Поставщик услуг подключения

Вы можете обратиться к поставщику услуг Интернета или к партнеру по обмену интернет-трафиком, чтобы получить Службу пиринга для подключения собственной сети к сети Майкрософт.

Убедитесь, что поставщики услуг подключения имеют партнерские отношения с корпорацией Майкрософт.

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Войдите в учетную запись Azure и выберите свою подписку.

Чтобы начать настройку, войдите в свою учетную запись Azure. Если вы используете параметр Cloud Shell **попробовать** , вы вошли автоматически. Используйте следующие примеры для подключения.

```azurecli-interactive
az login
```

Просмотрите подписки учетной записи.

```azurecli-interactive
az account list
```

Выберите подписку, для которой необходимо зарегистрировать подключение службы пиринга.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

Если у вас еще нет группы ресурсов, ее необходимо создать перед регистрацией подключения к службе пиринга. Чтобы создать группу ресурсов, выполните следующую команду:

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. Зарегистрируйте подписку с помощью поставщика ресурсов и флага функции.

Прежде чем перейти к действиям, описанным в статье регистрация подключения службы пиринга с помощью Azure CLI, зарегистрируйте подписку с помощью поставщика ресурсов и флага функции, используя Azure CLI. Команды Azure CLI указаны здесь:

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. Регистрация подключения к службе пиринга

Зарегистрируйте подключение службы пиринга, используя следующий набор команд с помощью Azure CLI. В этом примере регистрируется подключение к службе пиринга с именем Мипирингсервице.

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. зарегистрируйте префикс

Зарегистрируйте префикс, предоставленный поставщиком услуг подключения, выполнив следующие команды через Azure CLI. В этом примере регистрируется префикс с именем myPrefix.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о подключении службы пиринга см. в статье [Подключение к службе пиринга](connection.md).
- Дополнительные сведения о телеметрии см. в статье [Телеметрия для подключения Службы пиринга](connection-telemetry.md).
- Сведения об измерении телеметрии см. в статье [Измерение телеметрии для подключения Службы пиринга](measure-connection-telemetry.md).
- Чтобы зарегистрировать подключение с помощью Azure PowerShell, воспользуйтесь статьей [Регистрация подключения Службы пиринга (Azure PowerShell)](powershell.md).
- Чтобы зарегистрировать подключение с помощью портал Azure, см. статью [Регистрация подключения службы пиринга — портал Azure](azure-portal.md).
