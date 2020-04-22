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
ms.openlocfilehash: 000971878e24c46892aaef1fa0c65237a4219883
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678517"
---
После успешного развертывания ресурса **Peering** можно просмотреть его, выпустив следующие действия.

1. Перейдите к **группам ресурсов**и выберите выбранную группу ресурсов при создании ресурса **Peering.** Используйте поле **фильтра,** если у вас слишком много групп ресурсов.

    > [!div class="mx-imgBorder"]
    > ![Группы ресурсов](../media/setup-direct-get-resourcegroup.png)

1. Выберите созданный ресурс **Peering.**

    > [!div class="mx-imgBorder"]
    > ![Представление ресурсов](../media/setup-direct-get-open.png)

1. Страница **«Обзор»** показывает информацию высокого уровня, как показано здесь.

    > [!div class="mx-imgBorder"]
    > ![Панель обзора пиринга](../media/setup-exchange-get-overview.png)

1. Слева выберите **информацию ASN** для просмотра информации, представленной при создании PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![Информация о вглядываении ресурса ASN](../media/setup-direct-get-asninfo.png)

1. Слева выберите **Соединения**. В верхней части экрана вы видите резюме вглядываемых связей между ASN и Microsoft, на различных объектах в метро. Вы также можете получить доступ к резюме соединений со страницы **Обзор,** выбрав **соединения** в центральной панели, как показано на рисунке.

    > [!div class="mx-imgBorder"]
    > ![Подключение ресурсов пиринга](../media/setup-exchange-get-connectionssummary.png)

    * **Состояние соединения** соответствует состоянию установки пирингового соединения. Состояния, отображаемые в этом поле, следуют диаграмме состояния, показанной на [пошаговом пошаговом уходе Exchange.](../walkthrough-exchange-all.md)
    * **IPv4 Сессия государства** и **IPv6 Сессия государства** соответствуют IPv4 и IPv6 BGP государств, соответственно.  
    * При выборе строки в верхней части экрана раздел **Соединения** в нижней части отображает детали для каждого соединения. Выберите стрелки для расширения **конфигурации,** **адреса IPv4**и **адреса IPv6.**

    > [!div class="mx-imgBorder"]
    > ![Представление ресурсов](../media/setup-exchange-get-connectionsipv4.png)
