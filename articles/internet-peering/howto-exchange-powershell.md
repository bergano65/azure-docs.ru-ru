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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774410"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Создание или изменение пиринга Exchange с помощью PowerShell

В этой статье описывается, как создать пиринг Microsoft Exchange с помощью командлетов PowerShell и модели развертывания диспетчер ресурсов. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и отменить его предоставление.

При желании вы можете выполнить инструкции с помощью [портала](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Перед началом работы
* Прежде чем приступить к настройке, ознакомьтесь с [предварительными требованиями](prerequisites.md) и [пошаговым руководством по Exchange пиринга](walkthrough-exchange-all.md) .
* Если у вас есть пиринг Exchange с корпорацией Майкрософт, которые не будут преобразованы в ресурсы Azure, см. статью [Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью PowerShell](howto-legacy-exchange-powershell.md) .

### <a name="working-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Создание и инициализация пиринга Exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Войдите в учетную запись Azure и выберите подписку.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=exchange-location></a>Получение списка поддерживаемых расположений пиринга для пиринга Exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name=create></a>Создание пиринга Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name=get></a>Получение пиринга Exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify"></a>Изменение пиринга Exchange
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name=delete></a>Отменить подготовку пиринга Exchange

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение прямого пиринга с помощью PowerShell](howto-direct-powershell.md)
* [Преобразование устаревшего прямого пиринга в ресурс Azure с помощью PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Дополнительные ресурсы
Подробное описание всех параметров можно получить, выполнив следующую команду.

```powershell
Get-Help Get-AzPeering -detailed
```

Дополнительные сведения см. на странице [часто задаваемые вопросы об пиринга через Интернет](faqs.md)
