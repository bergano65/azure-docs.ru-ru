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
ms.openlocfilehash: 7639499aaef8d479c2552849b2124e709c46fd36
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680781"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Создание или изменение прямого пиринга с помощью PowerShell

В этой статье описывается создание прямого пиринга Майкрософт с помощью командлетов PowerShell и модели развертывания Azure Resource Manager. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и отменить его предоставление.

При желании вы можете выполнить это пошаговое руководством с помощью [портала](howto-direct-portal.md)Azure.

## <a name="before-you-begin"></a>Подготовка к работе
* Прежде чем начать настройку, ознакомьтесь с [предварительными требованиями](prerequisites.md) и [пошаговым руководством по непосредственному пирингу](walkthrough-direct-all.md) .
* Если у вас уже есть прямые пиринга с Майкрософт, которые не преобразованы в ресурсы Azure, см. статью [Преобразование устаревшего прямого пиринга в ресурс Azure с помощью PowerShell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Создание и предоставление прямого пиринга

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Войдите в учетную запись Azure и выберите подписку.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Получение списка поддерживаемых расположений пиринга для прямого пиринга
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Создание прямого пиринга
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Проверка прямого пиринга
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Изменение прямого пиринга
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Отменить подготовку прямого пиринга
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Дальнейшие шаги

* [Создание или изменение пиринга Exchange с помощью PowerShell](howto-exchange-powershell.md)
* [Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Дополнительные ресурсы
Подробное описание всех параметров можно получить, выполнив следующую команду.

```powershell
Get-Help Get-AzPeering -detailed
```

Дополнительные сведения см. в разделе [часто задаваемые вопросы об пиринга через Интернет](faqs.md).
