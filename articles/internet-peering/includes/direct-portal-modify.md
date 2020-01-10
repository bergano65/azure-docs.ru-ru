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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775346"
---
В этом разделе описывается выполнение следующих операций изменения для прямого пиринга.

### <a name="add-direct-peering-connections"></a>Добавление соединений с прямыми пиринга
1. Нажмите кнопку **+ добавить подключения** в верхней части экрана и настройте новое соединение пиринга.
    > [!div class="mx-imgBorder"]
    > ![представление ресурсов пиринга](../media/setup-direct-modify-addconnection.png)
1. Заполните форму **подключения прямого пиринга** и нажмите кнопку **сохранить**. Чтобы получить справку по настройке подключения пиринга, ознакомьтесь с инструкциями в разделе "Создание и инициализация прямого пиринга" выше.
    > [!div class="mx-imgBorder"]
    > ![представление ресурсов пиринга](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Удаление подключений прямого пиринга

Удаление подключения в настоящее время не поддерживается на портале. Обратитесь в службу [пиринга Майкрософт](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Обновление или понижение пропускной способности для активных соединений
1. Щелкните подключение пиринга, которое необходимо изменить, а затем нажмите кнопку **...**  > **изменить подключение** .
    > [!div class="mx-imgBorder"]
    > ![изменение подключения пиринга](../media/setup-direct-modify-editconnection.png)
1. Измените пропускную способность, как показано ниже, и нажмите кнопку **сохранить**.
    > [!div class="mx-imgBorder"]
    > ![одноранговое подключение изменить пропускную способность](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Добавить сеанс IPv4/IPv6 в активных соединениях.
1. Щелкните подключение пиринга, которое необходимо изменить, а затем нажмите кнопку **...**  > **изменить подключение** , как показано выше.
1. Добавьте сведения о префиксе **IPv4** или префиксе **сеанса IPv6** и нажмите кнопку **сохранить**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Удаление сеанса IPv4 или IPv6 на активных подключениях.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
