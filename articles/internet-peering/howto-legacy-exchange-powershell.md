---
title: Преобразование наследного вонючего Exchange в ресурс Azure с помощью PowerShell
titleSuffix: Azure
description: Преобразование наследного вонючего Exchange в ресурс Azure с помощью PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775398"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Преобразование наследного вонючего Exchange в ресурс Azure с помощью PowerShell

В этой статье описывается, как преобразовать существующую устаревшую Биржу, вглядывающуюся в ресурс Azure с помощью cmdlets PowerShell.

Если вы предпочитаете, вы можете завершить это руководство с помощью [портала](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Перед началом
* Просмотрите [предпосылки](prerequisites.md) и [обменпирирования пошаговое решение,](walkthrough-exchange-all.md) прежде чем начать конфигурацию.

### <a name="working-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Преобразование устаревшего пиринга через точку обмена в ресурс Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Войдите в учетную запись Azure и выберите подписку.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Получить наследие Exchange пиринг для преобразования
Ниже приведен пример, чтобы получить наследие Exchange пиринг в Сиэтле вглядываясь месте:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

Ответ выглядит примерно так:
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>Преобразование устаревшего пиринга
Ниже приводится команда для преобразования устаревшего Exchange, вглядываясь в ресурс Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Обратите внимание, что при преобразовании устаревшего пиринга в ресурс azure изменения не поддерживаются&nbsp;

Ниже приведен пример ответа, когда сквозная подготовка была успешно завершена:

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>Дополнительные ресурсы
Подробное описание всех параметров можно получить, выполнив следующую команду.

```powershell
Get-Help Get-AzPeering -detailed
```
Для получения дополнительной информации посетите [Интернет пиринг часто задаваемые вопросы](faqs.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение пиринга Exchange с помощью PowerShell](howto-exchange-powershell.md)
