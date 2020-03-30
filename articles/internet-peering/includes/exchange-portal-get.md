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
ms.openlocfilehash: b967c844ab145074490e931122cbe092d67de0c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774553"
---
После успешного развертывания ресурса **Peering** можно просмотреть его, выдвивая ниже приведенные ниже шаги.

1. Перейдите к **группам ресурсов** и нажмите на выбранную группу ресурсов при создании **ресурса Peering.** Вы можете использовать поле *фильтра,* если у вас слишком много групп ресурсов.

    > [!div class="mx-imgBorder"]
    > ![Группа ресурсов пиринга](../media/setup-direct-get-resourcegroup.png)

1. Нажмите на созданный ресурс **Peering.**

    > [!div class="mx-imgBorder"]
    > ![Представление ресурсов](../media/setup-direct-get-open.png)

1. Страница **«Обзор»** показывает информацию высокого уровня. Обратите внимание на информацию, приведенную ниже.

    > [!div class="mx-imgBorder"]
    > ![Представление ресурсов](../media/setup-exchange-get-overview.png)

1. Слева нажмите на **информацию ASN** для просмотра информации, представленной при создании PeerAsn

    > [!div class="mx-imgBorder"]
    > ![Представление ресурсов](../media/setup-direct-get-asninfo.png)

1. Слева щелкните по **ссылкам**. Наблюдайте сверху резюме внештатных связей между ASN и Microsoft на различных объектах метро. Вы также можете прийти к резюме соединений со страницы **Обзор,** нажав на **соединения** в центральной панели, как выделено выше.

    > [!div class="mx-imgBorder"]
    > ![Представление ресурсов](../media/setup-exchange-get-connectionssummary.png)

    * **Состояние соединения** соответствует состоянию настроенного соединения. Состояния, отображаемые в этом поле, следуют диаграмме состояния, показанной в [пошаговом пошаговом уходе Exchange](../walkthrough-exchange-all.md)
    * **IPv4 Сессия государства** и **IPv6 Сессия государства** соответствуют IPv4 и IPv6 BGP сессии государств соответственно.  
    * При выборе строки сверху раздел ***соединения*** в нижней части показывает детали для каждого соединения. Вы можете нажать на стрелки знаков, чтобы расширить ***подразделы Конфигурация***, ***IPv4 адрес*** и ***адрес IPv6***

    > [!div class="mx-imgBorder"]
    > ![Представление ресурсов](../media/setup-exchange-get-connectionsipv4.png)
