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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681076"
---
В этом разделе описывается, как выполнять следующие операции модификации для прямого пиринга.

### <a name="add-direct-peering-connections"></a>Добавление прямых пиринговых соединений
1. Выберите кнопку **«Добавить соединения»** и настройте новое пиринговое соединение.
    > [!div class="mx-imgBorder"]
    > ![Представление ресурсов](../media/setup-direct-modify-addconnection.png)

1. Заполните форму **прямого пиринга соединения** и выберите **Сохранить**. Для справки о настройке одноранговой связи просмотрите шаги в разделе "Создание и предоставление прямого пиринга".
    > [!div class="mx-imgBorder"]
    > ![Прямая форма подключения](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Удалить прямые соединения пиринга

Удаление соединения в настоящее время не поддерживается на портале Azure. Для получения дополнительной информации обратитесь в [грансь-наВеркинг е.](mailto:peeringexperience@microsoft.com)

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Обновление или понижение пропускной способности на активных соединениях
1. Выберите одноранговое соединение, из меняемые, а затем выберите **...**  >  **Отодвить соединение**.
    > [!div class="mx-imgBorder"]
    > ![Изменение подключения](../media/setup-direct-modify-editconnection.png)

1. Измените пропускную способность, переместив ползунок, а затем выберите **Сохранить**.
    > [!div class="mx-imgBorder"]
    > ![Изменение пропускной способности](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Добавление информации о сеансах IPv4 или IPv6 об активных соединениях
1. Выберите одноранговое соединение, из меняемые, а затем выберите **...**  >  **Отображать соединение,** как показано в шаге 1.
1. Введите **префикс сессии IPv4** или информацию **о префиксе Session IPv6** и выберите **Сохранить**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Удалите информацию о сеансах IPv4 или IPv6 об активных соединениях
Удаление **информации о префиксе Session IPv4** или **приставке Session IPv6** в настоящее время не поддерживается на портале. Для получения дополнительной информации обратитесь в [грансь-наВеркинг е.](mailto:peeringexperience@microsoft.com)
