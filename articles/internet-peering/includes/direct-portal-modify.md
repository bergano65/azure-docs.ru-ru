---
title: включить файл
titleSuffix: Azure
description: включить файл
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775346"
---
В этом разделе описывается, как выполнять следующие операции модификации для прямого пиринга:

### <a name="add-direct-peering-connections"></a>Добавление прямых пиринговых соединений
1. Нажмите на кнопку **«Добавить соединения»** сверху и настройте новое пиринговое соединение.
    > [!div class="mx-imgBorder"]
    > ![Представление ресурсов](../media/setup-direct-modify-addconnection.png)
1. Заполните **прямую вглядывающую** форму подключения и нажмите **Сохранить**. Для справки о настройке одноранговой связи просмотрите шаги в разделе "Создание и предоставление прямого пиринга".
    > [!div class="mx-imgBorder"]
    > ![Представление ресурсов](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Удалить прямые соединения пиринга

Удаление соединения в настоящее время не поддерживается на портале. Обратитесь в [экспертные штаты Майкрософт](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Обновление или понижение пропускной способности на активных соединениях
1. Нажмите на вглядывающее соединение, необходимое для изменения, а затем нажмите на **...**  >  **Кнопка подключения.**
    > [!div class="mx-imgBorder"]
    > ![Вглядывающее подключение Edit](../media/setup-direct-modify-editconnection.png)
1. Измените пропускную способность, как показано ниже, а затем нажмите **Сохранить**.
    > [!div class="mx-imgBorder"]
    > ![Вглядывающее соединение изменяет пропускную способность](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Добавьте сеанс IPv4/IPv6 на активных соединениях.
1. Нажмите на вглядывающее соединение, необходимое для изменения, а затем нажмите на **...**  >  **Отображать** кнопку соединения, как показано выше.
1. Добавить **приставку Сессии IPv4** или **сессию IPv6 приставка** информации и нажмите **Сохранить**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Удалите сеанс IPv4/IPv6 на активных соединениях.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
