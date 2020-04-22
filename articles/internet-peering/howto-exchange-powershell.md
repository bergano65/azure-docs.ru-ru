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
ms.openlocfilehash: 23c905f148da614c7785b61b76abed191206cd90
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678665"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Создание или изменение пиринга Exchange с помощью PowerShell

В этой статье описывается, как создать пиринг биржи Майкрософт с помощью cmdlets PowerShell и модели развертывания диспетчера ресурсов. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и дезавуировать его.

Если вы предпочитаете, вы можете заполнить это руководство с помощью [портала](howto-exchange-portal.md)Azure .

## <a name="before-you-begin"></a>Подготовка к работе
* Просмотрите [предпосылки](prerequisites.md) и [пошаговое пошаговое пошаговое пошаговое решение Exchange](walkthrough-exchange-all.md) перед началом конфигурации.
* Если у вас уже есть кондиционирования Exchange с корпорацией Майкрософт, которые не преобразуются в ресурсы Azure, см. [Преобразование устаревшей Exchange, вглядывающее в ресурс Azure, с помощью PowerShell.](howto-legacy-exchange-powershell.md)

### <a name="work-with-azure-powershell"></a>Работа с Azure PowerShell
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

## <a name="next-steps"></a>Следующие шаги

* [Создание или изменение прямого пиринга с помощью PowerShell](howto-direct-powershell.md)
* [Преобразование устаревшего прямого вонючего в ресурс Azure с помощью PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Дополнительные ресурсы
Подробное описание всех параметров можно получить, выполнив следующую команду.

```powershell
Get-Help Get-AzPeering -detailed
```

Для получения дополнительной информации, см [Интернет пиринг часто задаваемые вопросы](faqs.md).
