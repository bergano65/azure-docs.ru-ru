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
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774566"
---
В этом разделе описывается выполнение следующих операций изменения для прямого пиринга.

### <a name="add-exchange-peering-connections"></a>Добавление подключений пиринга Exchange

1. Нажмите кнопку **+ добавить подключения** в верхней части экрана и настройте новое соединение пиринга.
    > [!div class="mx-imgBorder"]
    > ![представление ресурсов пиринга](../media/setup-exchange-modify-addconnection.png)
1. Заполните форму **подключения пиринга Exchange** и нажмите кнопку **сохранить**. Чтобы получить справку по настройке подключения пиринга, ознакомьтесь с инструкциями в разделе "Создание и инициализация прямого пиринга" выше.
    > [!div class="mx-imgBorder"]
    > ![представление ресурсов пиринга](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Удаление подключений пиринга Exchange

1. Щелкните подключение пиринга, которое необходимо удалить, а затем нажмите кнопку **...**  > **удалить подключение** .
    > [!div class="mx-imgBorder"]
    > ![удаление подключения пиринга](../media/setup-exchange-modify-deleteconnection.png)
1. Введите идентификатор ресурса в поле **Подтверждение удаления** , как показано в выделенных полях, и нажмите кнопку **Удалить**.
    > [!div class="mx-imgBorder"]
    > ![одноранговое подключение Делетеконфирм](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Добавить сеанс IPv4/IPv6 на активных подключениях

1. Щелкните подключение пиринга, которое необходимо изменить, а затем нажмите кнопку **...**  > **изменить подключение** .
    > [!div class="mx-imgBorder"]
    > ![изменение подключения пиринга](../media/setup-exchange-modify-editconnection.png)
1. Добавьте сведения об адресе **IPv4** или **IPv6** и нажмите кнопку **сохранить**.
    > [!div class="mx-imgBorder"]
    > ![изменение подключения пиринга](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Удалить сеанс IPv4 или IPv6 на активных подключениях

Удаление сеанса IPv4 или IPv6 из существующего подключения в настоящее время не поддерживается на портале. Обратитесь в службу [пиринга Майкрософт](mailto:peeringexperience@microsoft.com).