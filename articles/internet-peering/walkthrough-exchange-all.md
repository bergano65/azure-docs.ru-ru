---
title: Пошаговое руководство по пирингу через точку обмена
titleSuffix: Azure
description: Пошаговое руководство по пирингу через точку обмена
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775177"
---
# <a name="exchange-peering-walkthrough"></a>Пошаговое руководство по пирингу через точку обмена

В этом разделе разъясняются шаги, которые необходимо выполнить для настройки и управления пирингом Exchange.

## <a name="create-an-exchange-peering"></a>Создание пиринга Exchange
> [!div class="mx-imgBorder"]
> ![Обменпиры рабочим и состояниями соединения](./media/exchange-peering.png)

Следующие шаги должны быть выполнены для того, чтобы обеспечить Exchange пиринг:
1. Просмотрите [политику вобласти в экспертное раны корпорации](https://peering.azurewebsites.net/peering) Майкрософт, чтобы понять требования к пирингу Exchange.
1. Найти местоположение пиринга майкрософт и идентификатор объекта peering в [PeeringDB](https://www.peeringdb.com/net/694)
1. Запрос Exchange пиринг для пирингового местоположения с помощью инструкций в [Создание и изменение Exchange пиринг с помощью powerShell](howto-exchange-powershell.md) статьи для более подробной информации.
1. После отправки запроса на одноранговой запрос корпорация Майкрософт рассмотрит запрос и при необходимости свяжется с вами.
1. После утверждения изменения состояния соединения в Утвержденном
1. Наймите сессию BGP в конце и уведомите майкрософт
1. Мы будем предоставлять BGP сессии с DENY ВСЕ политики и проверки сквозной.
1. В случае успеха вы получите уведомление о том, что состояние соединения является активным.
1. Трафик будет разрешен через новый пиринг.

Обратите внимание, что состояния соединения не следует путать со стандартными состояниями сеанса [BGP.](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Преобразование устаревшего пиринга через точку обмена в ресурс Azure
Необходимо выйти за собой следующие шаги для преобразования наследного ресурса Exchange в ресурс Azure:
1. Следуйте инструкциям в [Convert a legacy Exchange, вглядываясь](howto-legacy-exchange-powershell.md) в ресурс Azure
1. После отправки запроса на конверсию корпорация Майкрософт рассмотрит запрос и при необходимости свяжется с вами.
1. После утверждения вы увидите, что ваш Exchange вглядывается в состояние соединения как Active.

## <a name="deprovision-exchange-peering"></a>Обмен деадом
Свяжитесь с [корпорацией Майкрософт,](mailto:peering@microsoft.com) чтобы сделать для де-продиктора Exchange.

Когда вглядывание Exchange настроено на дедиспрепром, состояние соединения будет видеться как **PendingRemove**

> [!NOTE]
> Если вы запустите PowerShell cmdlet для удаления вглядываемого Exchange, когда состояние соединения provisioningStarted или ProvisioningCompleted операция выйдет из строя.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте о [предпосылках для настройки пиринга с помощью Microsoft.](prerequisites.md)
