---
title: Создание или изменение пиринга Exchange с помощью PowerShell
titleSuffix: Azure
description: Создание или изменение пиринга Exchange с помощью PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774410"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Создание или изменение пиринга Exchange с помощью PowerShell

В этой статье описывается, как создать пиринг биржи Майкрософт с помощью cmdlets PowerShell и модели развертывания диспетчера ресурсов. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и дезавуировать его.

Если вы предпочитаете, вы можете завершить это руководство с помощью [портала](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Перед началом
* Просмотрите [предпосылки](prerequisites.md) и [обменпирирования пошаговое решение,](walkthrough-exchange-all.md) прежде чем начать конфигурацию.
* В случае, если у вас уже есть кондиционирования Exchange с корпорацией Майкрософт, которые не преобразуются в ресурсы Azure, обратитесь [к преобразованию устаревшей Exchange, вглядывающейся в ресурс Azure с помощью PowerShell](howto-legacy-exchange-powershell.md)

### <a name="working-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Создание и предоставление пиринга Exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Войдите в учетную запись Azure и выберите подписку.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Получить список поддерживаемых местоположений пиринга exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Создание пиринга Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Получить Exchange вглядываясь
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Изменение пиринга Биржи
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Деобеспечение биры пиринг

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение прямого пиринга с помощью PowerShell](howto-direct-powershell.md)
* [Преобразование устаревшего прямого врани в ресурс Azure с помощью PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Дополнительные ресурсы
Подробное описание всех параметров можно получить, выполнив следующую команду.

```powershell
Get-Help Get-AzPeering -detailed
```

Для получения дополнительной информации посетите [Интернет пиринг часто задаваемые вопросы](faqs.md)
