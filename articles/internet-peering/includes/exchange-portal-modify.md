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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774566"
---
В этом разделе описывается, как выполнять следующие операции модификации для прямого пиринга:

### <a name="add-exchange-peering-connections"></a>Добавление внравных соединений Exchange

1. Нажмите на кнопку **«Добавить соединения»** сверху и настройте новое пиринговое соединение.
    > [!div class="mx-imgBorder"]
    > ![Представление ресурсов](../media/setup-exchange-modify-addconnection.png)
1. Заполните форму **вглядывающего подключения Exchange** и нажмите **Сохранить**. Для справки о настройке одноранговой связи просмотрите шаги в разделе "Создание и предоставление прямого пиринга".
    > [!div class="mx-imgBorder"]
    > ![Представление ресурсов](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Удалить соединения пиринга Exchange

1. Нажмите на вглядывающее соединение, необходимое для удаления, а затем нажмите на **...**  > Удалить кнопку **подключения.**
    > [!div class="mx-imgBorder"]
    > ![Удаление подключения](../media/setup-exchange-modify-deleteconnection.png)
1. Введите идентификатор ресурса в поле **Подтвердите удаление,** как показано в выделенных коробках, и нажмите **Удалить**.
    > [!div class="mx-imgBorder"]
    > ![Вглядывающее соединение УдалитьПодтверждение](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Добавление сеанса IPv4/IPv6 на активных соединениях

1. Нажмите на вглядывающее соединение, необходимое для изменения, а затем нажмите на **...**  >  **Кнопка подключения.**
    > [!div class="mx-imgBorder"]
    > ![Вглядывающее подключение Edit](../media/setup-exchange-modify-editconnection.png)
1. Добавьте **адрес IPv4** или **информацию о адресе IPv6** и нажмите **Сохранить**.
    > [!div class="mx-imgBorder"]
    > ![Изменение врал-соединения](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Удалить сеанс IPv4/IPv6 на активных соединениях

Удаление сеанса IPv4/IPv6 из существующего соединения в настоящее время не поддерживается на портале. Обратитесь в [экспертные штаты Майкрософт](mailto:peeringexperience@microsoft.com).