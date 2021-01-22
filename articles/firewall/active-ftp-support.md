---
title: Поддержка Active FTP в брандмауэре Azure
description: По умолчанию активный FTP отключен в брандмауэре Azure. Его можно включить с помощью шаблонов PowerShell, CLI и ARM.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/21/2021
ms.author: victorh
ms.openlocfilehash: 2ff61d06885c182454c99ee7e982a3a1a1f5013c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690710"
---
# <a name="azure-firewall-active-ftp-support"></a>Поддержка Active FTP в брандмауэре Azure

При использовании Active FTP сервер FTP инициирует подключение данных к назначенному порту данных FTP-клиента. Брандмауэры в сети на стороне клиента обычно блокируют внешний запрос на подключение к внутреннему порту клиента. Дополнительные сведения см. [в разделе Active FTP и пассивный FTP, подробное объяснение](https://slacksite.com/other/ftp.html).

По умолчанию поддержка Active FTP в брандмауэре Azure отключена для защиты от атак с использованием FTP-сервера с помощью `PORT` команды FTP. Однако можно включить Active FTP при развертывании с помощью Azure PowerShell, Azure CLI или шаблона Azure ARM.

## <a name="azure-powershell"></a>Azure PowerShell

Чтобы выполнить развертывание с помощью Azure PowerShell, используйте `AllowActiveFTP` параметр. Дополнительные сведения см. в разделе [Создание брандмауэра с разрешающим активным FTP](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>Azure CLI

Чтобы выполнить развертывание с помощью Azure CLI, используйте `--allow-active-ftp` параметр. Дополнительные сведения см. в статье [AZ Network Firewall Create](/cli/azure/ext/azure-firewall/network/firewall?view=azure-cli-latest#ext_azure_firewall_az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Шаблон Azure Resource Manager (ARM)

Чтобы выполнить развертывание с помощью шаблона ARM, используйте `AdditionalProperties` поле:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Дополнительные сведения см. в разделе [Microsoft. Network азурефиреваллс](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Следующие шаги

Сведения о развертывании брандмауэра Azure см. в статье [развертывание и настройка брандмауэра Azure с помощью Azure PowerShell](deploy-ps.md).