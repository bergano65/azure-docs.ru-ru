---
title: Включение службы пиринга Azure на прямом одноранговом узле с помощью PowerShell
titleSuffix: Azure
description: Включение службы пиринга Azure на прямом одноранговом узле с помощью PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686965"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Включение службы пиринга Azure на прямом одноранговом узле с помощью PowerShell

В этой статье описывается, как включить [службу пиринга](overview-peering-service.md) Azure на непосредственном одноранговом пиринге с помощью командлетов PowerShell и модели развертывания Azure Resource Manager.

При желании вы можете выполнить это пошаговое руководством с помощью [портала](howto-peering-service-portal.md)Azure.

## <a name="before-you-begin"></a>Подготовка к работе
* Прежде чем приступить к настройке, ознакомьтесь с [предварительными требованиями](prerequisites.md) .
* Выберите прямой пиринг в подписке, для которой требуется включить службу пиринга. Если у вас ее нет, преобразуйте устаревший прямой пиринг или создайте новый прямой пиринг:
    * Чтобы преобразовать устаревший прямой пиринг, следуйте инструкциям в статье [Преобразование устаревшего прямого пиринга в ресурс Azure с помощью PowerShell](howto-legacy-direct-powershell.md).
    * Чтобы создать новый прямой пиринг, следуйте инструкциям в статье [Создание или изменение прямого пиринга с помощью PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Включение прямого пиринга в Службе пиринга

### <a name="view-direct-peering"></a><a name= get></a>Просмотр прямого пиринга
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Включение прямого пиринга для службы пиринга

После получения прямого пиринга на предыдущем шаге включите его для службы пиринга.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Изменение соединения с прямым пирингом

Если необходимо изменить параметры подключения, см. раздел "изменение прямого пиринга" статьи [Создание или изменение прямого пиринга с помощью PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Дальнейшие шаги

* [Создание или изменение пиринга Exchange с помощью PowerShell](howto-exchange-powershell.md)
* [Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Дополнительные ресурсы
Подробное описание всех параметров можно получить, выполнив следующую команду.

```powershell
Get-Help Get-AzPeering -detailed
```

Часто задаваемые вопросы см. в статье [часто ЗАдаваемые вопросы о службе пиринга](service-faqs.md).