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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774241"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Создание или изменение прямого пиринга с помощью PowerShell

В этой статье описывается создание прямого пиринга Майкрософт с помощью командлетов PowerShell и модели развертывания диспетчер ресурсов. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и отменить его предоставление.

При желании вы можете выполнить инструкции с помощью [портала](howto-direct-portal.md).

## <a name="before-you-begin"></a>Перед началом работы
* Перед началом настройки проверьте [Предварительные требования](prerequisites.md) и [Пошаговое руководство по непосредственному пирингу](walkthrough-direct-all.md) .
* Если вы уже используете прямой пиринг с корпорацией Майкрософт, который не преобразуется в ресурсы Azure, см. статью [Преобразование устаревшего прямого пиринга в ресурс Azure с помощью PowerShell](howto-legacy-direct-powershell.md) .

### <a name="working-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Создание и предоставление прямого пиринга

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Войдите в учетную запись Azure и выберите подписку.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=direct-location></a>Получение списка поддерживаемых расположений пиринга для прямого пиринга
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name=create></a>Создание прямого пиринга
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name=get></a>Проверка прямого пиринга
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify"></a>Изменение прямого пиринга
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="delete"></a>Отменить подготовку прямого пиринга
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение пиринга Exchange с помощью PowerShell](howto-exchange-powershell.md).
* [Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью PowerShell](howto-legacy-exchange-powershell.md).

## <a name="additional-resources"></a>Дополнительные ресурсы
Подробное описание всех параметров можно получить, выполнив следующую команду.

```powershell
Get-Help Get-AzPeering -detailed
```

Дополнительные сведения см. на странице [часто задаваемые вопросы об пиринга через Интернет](faqs.md)
