---
title: Включение службы пиринга на прямом одноранговом узле с помощью портала
titleSuffix: Azure
description: Включение службы пиринга на прямом одноранговом узле с помощью портала
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d6b67c42ef8a5ba5ae98894775d1f56cee39ba8b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774982"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Включение службы пиринга на прямом одноранговом узле с помощью портала

В этой статье описывается, как включить [службу пиринга](overview-peering-service.md) на прямом одноранговом узле с помощью портала.

При желании вы можете выполнить инструкции с помощью [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Перед началом работы
* Прежде чем приступить к настройке, ознакомьтесь с [предварительными требованиями](prerequisites.md) .
* Выберите прямой пиринг в подписке, в которой вы хотите включить службу пиринга. Если у вас ее нет, либо преобразуйте устаревший прямой пиринг, либо создайте новый прямой пиринг.
    * Чтобы преобразовать устаревший прямой пиринг, следуйте инструкциям в статье [Преобразование устаревшего прямого пиринга в ресурс Azure с помощью портала](howto-legacy-direct-portal.md).
    * Чтобы создать новый прямой пиринг, следуйте инструкциям в статье [Создание или изменение прямого пиринга с помощью портала](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Включение службы пиринга на непосредственном одноранговом пиринге

### <a name= get></a>Просмотр прямого пиринга
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name= get></a>Включение прямого пиринга для службы пиринга

После открытия прямого пиринга на предыдущем шаге включите его для службы пиринга.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Изменение соединения с прямым пирингом

Если необходимо изменить параметры подключения, см. раздел **изменение прямого пиринга** в статье [Создание или изменение прямого пиринга с помощью портала](howto-direct-portal.md) .

## <a name="next-steps"></a>Дальнейшие действия

* [Создание или изменение пиринга Exchange с помощью портала](howto-exchange-portal.md)
* [Преобразование устаревшего пиринга Exchange в ресурс Azure с помощью портала](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

Часто задаваемые вопросы см. в разделе [часто ЗАдаваемые вопросы о службе пиринга](service-faqs.md).