---
title: Создание экземпляра службы управления API Azure с помощью PowerShell | Документация Майкрософт
description: Следуя инструкциям из этого руководства, можно создать экземпляр службы управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: 7f316cd24bbb7b00c263566efdd81805c79f2e48
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66141274"
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Создание экземпляра службы управления API Azure

Служба управления API Azure помогает организациям публиковать API-интерфейсы для внешних пользователей, партнеров и собственных разработчиков, раскрывая таким образом потенциал своих данных и услуг. Служба управления API предоставляет базовые возможности для успешного выполнения программы API за счет привлечения разработчиков, бизнес-аналитики, анализа, безопасности и защищенности. Служба управления API позволяет создавать современные шлюзы API для существующих серверных служб, размещенных в любом месте, и управлять ими. Дополнительные сведения см. в разделе [общих сведений](api-management-key-concepts.md).

В этом кратком руководстве описаны шаги по созданию экземпляра службы управления API с помощью скриптов PowerShell. В этом кратком руководстве объясняется, как использовать службу **Azure Cloud Shell**, которую можно запустить с портала Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу https://portal.azure.com.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль Azure PowerShell 1.0 или последующих версий. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-Az-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzAccount`, чтобы создать подключение к Azure.


## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>Создание службы управления API

Это длительная операция, которая может занять до 15 минут.

```azurepowershell-interactive
New-AzApiManagement -ResourceGroupName "myResourceGroup" -Location "West US" -Name "apim-name" -Organization "myOrganization" -AdminEmail "myEmail" -Sku "Developer"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов и все связанные с ней ресурсы, выполнив команду [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Импорт и публикация первого API](import-and-publish.md)
