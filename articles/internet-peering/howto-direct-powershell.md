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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680781"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Создание или изменение прямого пиринга с помощью PowerShell

В этой статье описывается, как создать пиринг Microsoft Direct с помощью cmdlets PowerShell и модели развертывания ресурсов Azure. В этой статье также показано, как проверить состояние ресурса, обновить его или удалить и дезавуировать его.

Если вы предпочитаете, вы можете заполнить это руководство с помощью [портала](howto-direct-portal.md)Azure .

## <a name="before-you-begin"></a>Подготовка к работе
* Просмотрите [предпосылки](prerequisites.md) и [пошаговое руководство Direct peering](walkthrough-direct-all.md) перед началом конфигурации.
* Если у вас уже есть прямые вглядовые связи с корпорацией Майкрософт, которые не преобразуются в ресурсы Azure, [см. Преобразуйте устаревающее прямое использование ресурса Azure с помощью PowerShell.](howto-legacy-direct-powershell.md)

### <a name="work-with-azure-powershell"></a>Работа с Azure PowerShell
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

## <a name="next-steps"></a>Следующие шаги

* [Создание или изменение пиринга Exchange с помощью PowerShell](howto-exchange-powershell.md)
* [Преобразование наследного вглядывания в ресурс Azure с помощью PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Дополнительные ресурсы
Подробное описание всех параметров можно получить, выполнив следующую команду.

```powershell
Get-Help Get-AzPeering -detailed
```

Для получения дополнительной информации, см [Интернет пиринг часто задаваемые вопросы](faqs.md).
