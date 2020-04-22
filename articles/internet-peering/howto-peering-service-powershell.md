---
title: Включить службу пиринга Azure при прямом пиринге с помощью PowerShell
titleSuffix: Azure
description: Включить службу пиринга Azure при прямом пиринге с помощью PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686965"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Включить службу пиринга Azure при прямом пиринге с помощью PowerShell

В этой статье описывается, как включить [службу пиринга](overview-peering-service.md) Azure при прямой вглядывающей с помощью cmdlets PowerShell и модели развертывания ресурсов Azure.

Если вы предпочитаете, вы можете заполнить это руководство с помощью [портала](howto-peering-service-portal.md)Azure .

## <a name="before-you-begin"></a>Подготовка к работе
* Просмотрите [предпосылки](prerequisites.md) перед началом конфигурации.
* Выберите прямую вонючую в подписке, для которой вы хотите включить Peering Service. Если у вас его нет, либо конвертировать устаревшее прямое пиринг или создать новый прямой пиринг:
    * Чтобы преобразовать устаревшую вонючесть Direct, следуйте инструкциям в [Convert a legacy Direct, вглядываясь в ресурс Azure с помощью PowerShell.](howto-legacy-direct-powershell.md)
    * Чтобы создать новый Прямой пиринг, следуйте инструкциям в [Создать или изменить прямой пиринг с помощью PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Включение прямого пиринга в Службе пиринга

### <a name="view-direct-peering"></a><a name= get></a>Просмотр прямого пиринга
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Включить прямое пиринговое обслуживание для peering Service

После того, как вы получите Прямой пиринг на предыдущем этапе, включите его для Peering Service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Изменение прямого пирингового соединения

Если вам нужно изменить настройки соединения, см. раздел "Изменить прямое пиринг" в [create или изменить прямое пиринг с помощью PowerShell.](howto-direct-powershell.md)

## <a name="next-steps"></a>Следующие шаги

* [Создание или изменение пиринга Exchange с помощью PowerShell](howto-exchange-powershell.md)
* [Преобразование наследного вглядывания в ресурс Azure с помощью PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Дополнительные ресурсы
Подробное описание всех параметров можно получить, выполнив следующую команду.

```powershell
Get-Help Get-AzPeering -detailed
```

Для часто задаваемых вопросов, [см.](service-faqs.md)