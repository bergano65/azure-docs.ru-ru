---
title: Включение службы пиринга на прямом одноранговом узле с помощью PowerShell
titleSuffix: Azure
description: Включение службы пиринга на прямом одноранговом узле с помощью PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774189"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Включение службы пиринга на прямом одноранговом узле с помощью PowerShell

В этой статье описывается, как включить [службу пиринга](overview-peering-service.md) на прямом одноранговом пиринге с помощью командлетов PowerShell и модели развертывания Диспетчер ресурсов.

При желании вы можете выполнить инструкции с помощью [портала](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Перед началом работы
* Прежде чем приступить к настройке, ознакомьтесь с [предварительными требованиями](prerequisites.md) .
* Выберите прямой пиринг в подписке, в которой вы хотите включить службу пиринга. Если у вас ее нет, либо преобразуйте устаревший прямой пиринг, либо создайте новый прямой пиринг.
    * Чтобы преобразовать устаревший прямой пиринг, следуйте инструкциям в статье [Преобразование устаревшего прямого пиринга в ресурс Azure с помощью PowerShell](howto-legacy-direct-powershell.md).
    * Чтобы создать новый прямой пиринг, следуйте инструкциям в статье [Создание или изменение прямого пиринга с помощью PowerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Работа с Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Включение службы пиринга на непосредственном одноранговом пиринге

### <a name= get></a>Просмотр прямого пиринга
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name= get></a>Включение прямого пиринга для службы пиринга

После получения прямого пиринга на предыдущем шаге включите его для службы пиринга.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Изменение соединения с прямым пирингом

Если необходимо изменить параметры подключения, см. раздел **изменение прямого пиринга** в статье [Создание или изменение прямого пиринга с помощью PowerShell](howto-direct-powershell.md) .

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение пиринга Exchange с помощью PowerShell](howto-exchange-powershell.md)
* [Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Дополнительные ресурсы
Подробное описание всех параметров можно получить, выполнив следующую команду.

```powershell
Get-Help Get-AzPeering -detailed
```

Часто задаваемые вопросы см. в разделе [часто ЗАдаваемые вопросы о службе пиринга](service-faqs.md).