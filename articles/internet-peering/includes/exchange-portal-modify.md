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
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680925"
---
В этом разделе описывается, как выполнять следующие операции модификации для прямого пиринга.

### <a name="add-exchange-peering-connections"></a>Добавление внравных соединений Exchange

1. Выберите кнопку **«Добавить соединения»** и настройте новое пиринговое соединение.
    > [!div class="mx-imgBorder"]
    > ![Представление ресурсов](../media/setup-exchange-modify-addconnection.png)
1. Заполните форму **вглядывающего подключения Exchange** и выберите **Сохранить**. Для справки о настройке одноранговой связи просмотрите шаги в разделе "Создание и предоставление прямого пиринга".
    > [!div class="mx-imgBorder"]
    > ![Форма подключения Exchange Peering](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Удалить соединения пиринга Exchange

1. Выберите одноранговое соединение, необходимое для удаления, а затем выберите **...**  >  **Удаление соединения**.
    > [!div class="mx-imgBorder"]
    > ![Кнопка подключения](../media/setup-exchange-modify-deleteconnection.png)
1. Введите идентификатор ресурса в поле **«Подтвердить удалить»** и **выберите Delete**.
    > [!div class="mx-imgBorder"]
    > ![Удаление подтверждения](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Добавление сеанса IPv4 или IPv6 на активных соединениях

1. Выберите одноранговое соединение, из меняемые, а затем выберите **...**  >  **Отодвить соединение**.
    > [!div class="mx-imgBorder"]
    > ![Кнопка подключения](../media/setup-exchange-modify-editconnection.png)
1. Добавьте **адрес IPv4** или адресную информацию **IPv6** и выберите **Сохранить**.
    > [!div class="mx-imgBorder"]
    > ![Вглядывающиеся изменения соединения](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Удалить сеанс IPv4 или IPv6 на активных соединениях

Удаление сеанса IPv4 или IPv6 из существующего соединения в настоящее время не поддерживается на портале. Для получения дополнительной информации обратитесь в [грансь-наВеркинг е.](mailto:peeringexperience@microsoft.com)