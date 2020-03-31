---
title: Включить услугу пиринга при прямом пиринге с помощью PowerShell
titleSuffix: Azure
description: Включить услугу пиринга при прямом пиринге с помощью PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774189"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Включить услугу пиринга при прямом пиринге с помощью PowerShell

В этой статье описывается, как включить [peering Service](overview-peering-service.md) на прямой пиринг с помощью PowerShell cmdlets и модели развертывания менеджера ресурсов.

Если вы предпочитаете, вы можете завершить это руководство с помощью [портала](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Перед началом
* Просмотрите предпосылки перед началом [настройки.](prerequisites.md)
* Выберите прямую вонючую в подписке, на что вы хотите включить службу пиринга. Если у вас его нет, либо конвертировать устаревшее прямое пиринг или создать новый Direct peering.
    * Чтобы преобразовать устаревшую вонючесть Direct, следуйте инструкциям в [Convert a legacy Direct, вглядываясь в ресурс Azure с помощью PowerShell.](howto-legacy-direct-powershell.md)
    * Чтобы создать новый Прямой пиринг, следуйте инструкциям в [Создать или изменить прямой пиринг с помощью PowerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Включение прямого пиринга в Службе пиринга

### <a name="view-direct-peering"></a><a name= get></a>Просмотр прямого пиринга
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Включить прямое пиринговое обслуживание для peering Service

После получения Direct пиринга на предыдущем этапе включите его для Peering Service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Изменение прямого пирингового соединения

Если вам нужно изменить настройки соединения, обратитесь к изменению раздела **Direct peering** в [Create или изменять прямое пиринг с помощью PowerShell](howto-direct-powershell.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение пиринга Exchange с помощью PowerShell](howto-exchange-powershell.md)
* [Преобразование наследного вонючего Exchange в ресурс Azure с помощью PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Дополнительные ресурсы
Подробное описание всех параметров можно получить, выполнив следующую команду.

```powershell
Get-Help Get-AzPeering -detailed
```

Для часто задаваемых вопросов, [см.](service-faqs.md)