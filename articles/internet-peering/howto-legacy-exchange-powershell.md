---
title: Преобразование наследного вглядывания в ресурс Azure с помощью PowerShell
titleSuffix: Azure
description: Преобразование наследного вглядывания в ресурс Azure с помощью PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: eedf87548d62e05d4940911ed3dcd821077acb27
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686789"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>Преобразование наследного вглядывания в ресурс Azure с помощью PowerShell

В этой статье описывается, как преобразовать существующую устаревшую Биржу, вглядывающуюся в ресурс Azure, с помощью cmdlets PowerShell.

Если вы предпочитаете, вы можете заполнить это руководство с помощью [портала](howto-legacy-exchange-portal.md)Azure .

## <a name="before-you-begin"></a>Подготовка к работе
* Просмотрите [предпосылки](prerequisites.md) и [пошаговое пошаговое пошаговое пошаговое решение Exchange](walkthrough-exchange-all.md) перед началом конфигурации.

### <a name="work-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Преобразование наследного элемента Exchange в ресурс Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Войдите в учетную запись Azure и выберите подписку.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Получить наследование Exchange для преобразования
В этом примере показано, как получить устаревшую Биржу, вглядывающуюся в место всматриваемых местах Сиэтла:

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
Эта команда может использоваться для преобразования узлового элемента Exchange в ресурс Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> При преобразовании устаревшего вонючего в ресурс Azure изменения не поддерживаются.
&nbsp;

Этот пример ответа показывает, когда сквозная подготовка была успешно завершена:

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
Для получения дополнительной информации, см [Интернет пиринг часто задаваемые вопросы](faqs.md).

## <a name="next-steps"></a>Следующие шаги

* [Создание или изменение пиринга Exchange с помощью PowerShell](howto-exchange-powershell.md)
