---
title: Создание или изменение прямого пиринга с помощью PowerShell
titleSuffix: Azure
description: Создание или изменение прямого пиринга с помощью PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774241"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Создание или изменение прямого пиринга с помощью PowerShell

В этой статье описывается, как создать пиринг Microsoft Direct с помощью cmdlets PowerShell и модели развертывания диспетчера ресурсов. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и дезавуировать его.

Если вы предпочитаете, вы можете завершить это руководство с помощью [портала](howto-direct-portal.md).

## <a name="before-you-begin"></a>Перед началом
* Просмотрите [предпосылки](prerequisites.md) и [прямое пошаговое руководство](walkthrough-direct-all.md) перед началом конфигурации.
* В случае, если у вас уже есть Direct peering с Microsoft, которые не преобразуются в ресурсы Azure, обратитесь к [Преобразованию устаревшего Прямого визирования ресурса Azure с помощью PowerShell](howto-legacy-direct-powershell.md)

### <a name="working-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Создание и предоставление прямого пиринга

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Войдите в учетную запись Azure и выберите подписку.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Получить список поддерживаемых местоположений пиринга для прямого пиринга
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Создание прямого пиринга
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Проверить прямой пиринг
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Изменение прямого пиринга
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Деобеспечение прямого пиринга
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение пиринга Exchange с помощью PowerShell.](howto-exchange-powershell.md)
* [Преобразуйте устаревший обмен висящий в ресурс Azure с помощью PowerShell.](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Дополнительные ресурсы
Подробное описание всех параметров можно получить, выполнив следующую команду.

```powershell
Get-Help Get-AzPeering -detailed
```

Для получения дополнительной информации посетите [Интернет пиринг часто задаваемые вопросы](faqs.md)
