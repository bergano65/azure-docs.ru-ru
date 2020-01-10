---
title: Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью PowerShell
titleSuffix: Azure
description: Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775398"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью PowerShell

В этой статье описывается, как преобразовать имеющийся устаревший пиринг Exchange в ресурс Azure с помощью командлетов PowerShell.

При желании вы можете выполнить инструкции с помощью [портала](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Перед началом работы
* Прежде чем приступить к настройке, ознакомьтесь с [предварительными требованиями](prerequisites.md) и [пошаговым руководством по Exchange пиринга](walkthrough-exchange-all.md) .

### <a name="working-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Преобразование устаревшего пиринга Exchange в ресурс Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Войдите в учетную запись Azure и выберите подписку.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>Получение устаревшего пиринга Exchange для преобразования
Ниже приведен пример для получения устаревшего пиринга Exchange в расположении пиринга в Сиэтле:

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
Приведенную ниже команду можно использовать для преобразования устаревшего пиринга Exchange в ресурс Azure.

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Обратите внимание, что при преобразовании устаревшего пиринга в ресурс Azure изменения не поддерживаются &nbsp;

Ниже приведен пример ответа после успешного завершения подготовки к завершению.

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
Дополнительные сведения см. на странице [часто задаваемые вопросы об пиринга через Интернет](faqs.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение пиринга Exchange с помощью PowerShell](howto-exchange-powershell.md)
