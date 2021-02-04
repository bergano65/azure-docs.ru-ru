---
title: 'Azure ExpressRoute: Настройка Global Reach с помощью портал Azure'
description: Эта статья поможет вам связать каналы ExpressRoute вместе, чтобы создать частную сеть между локальными сетями и включить Global Reach с помощью портал Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 8366978d50875389ce872c2d1402f0defa2a7371
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539355"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>Настройка Global Reach ExpressRoute с помощью портал Azure

Эта статья поможет вам настроить ExpressRoute Global Reach с помощью PowerShell. Дополнительные сведения см. в разделе [Связывание каналов ExpressRoute для включения ExpressRoute Global Reach (предварительная версия)](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Подготовка к работе

Прежде чем начать настройку, подтвердите следующие критерии.

* Вы понимаете [рабочие процессы](expressroute-workflows.md)подготовки канала ExpressRoute.
* Каналы ExpressRoute находятся в подготовленном состоянии.
* Частный пиринг Azure настроен для каналов ExpressRoute.
* Если вы хотите запустить PowerShell локально, убедитесь, что на компьютере установлена последняя версия Azure PowerShell.

## <a name="identify-circuits"></a>Выявление цепей

1. В браузере откройте [портал Azure](https://portal.azure.com) и выполните вход с помощью учетной записи Azure.

2. Укажите каналы ExpressRoute, которые вы хотите использовать. Вы можете включить ExpressRoute Global Reach между частным пирингом двух каналов ExpressRoute, если они находятся в поддерживаемых странах и регионах. Каналы должны быть созданы в разных расположениях пиринга. 

   * Если подписке принадлежат оба канала, вы можете выбрать канал для выполнения конфигурации в следующих разделах.
   * Если два канала находятся в разных подписках Azure, может потребоваться выполнить авторизацию из одной подписки Azure. Затем необходимо передать ключ авторизации при выполнении команды конфигурации в другой подписке Azure.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="Список каналов ExpressRoute":::

## <a name="enable-connectivity"></a>Включение соединения

Обеспечение подключения между локальными сетями. Существуют отдельные наборы инструкций для каналов, которые находятся в одной подписке Azure, и цепи, которые являются разными подписками.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Каналы ExpressRoute в одной подписке Azure

1. Выберите конфигурацию **частного пиринга Azure** . 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Общие сведения об пиринга ExpressRoute":::

1. Установите флажок **включить Global REACH** и нажмите кнопку **Добавить Global REACH** , чтобы открыть страницу *Добавление Global REACH* конфигурации.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Включить глобальный доступ от частного пиринга":::

1. На странице *Добавление конфигурации Global REACH* присвойте имя этой конфигурации. Выберите *канал ExpressRoute* , к которому нужно подключить этот канал, и введите адрес **IPv4** для *Global REACH подсети*. Мы используем IP-адреса в этой подсети, чтобы установить подключение между двумя каналами ExpressRoute. Не используйте адреса из этой подсети в виртуальных сетях Azure или в локальной сети. Нажмите кнопку **Добавить** , чтобы добавить канал в конфигурацию частного пиринга.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Страница &quot;Конфигурация Global Reach&quot;":::

1. Нажмите кнопку **сохранить** , чтобы завершить настройку Global REACH. По завершении операции вы получите подключение между двумя локальными сетями с помощью обеих каналов ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Сохранение конфигурации частного пиринга":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Каналы ExpressRoute в разных подписках Azure

Если две цепи находятся не в одной подписке Azure, вам потребуется авторизация. В следующей конфигурации авторизация создается из подписки канала 2. Затем ключ авторизации передается цепи 1.

1. Создайте ключ авторизации.

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="Создать ключ авторизации"::: 

   Запишите идентификатор ресурса канала 2 и ключ авторизации.

1. Выберите конфигурацию **частного пиринга Azure** . 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Обзор пиринга с каналом 1":::

1. Установите флажок **включить Global REACH** и нажмите кнопку **Добавить Global REACH** , чтобы открыть страницу *Добавление Global REACH* конфигурации.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Включить глобальный доступ из цепи 1":::

1. На странице *Добавление конфигурации Global REACH* присвойте имя этой конфигурации. Установите флажок **авторизации активации** . Введите **ключ авторизации** и **идентификатор канала ExpressRoute** , созданный и полученный на шаге 1. Затем укажите **/29 IPv4** для *подсети Global REACH*. Мы используем IP-адреса в этой подсети, чтобы установить подключение между двумя каналами ExpressRoute. Не используйте адреса из этой подсети в виртуальных сетях Azure или в локальной сети. Нажмите кнопку **Добавить** , чтобы добавить канал в конфигурацию частного пиринга.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="Добавление Global Reach с ключом авторизации":::

1. Нажмите кнопку **сохранить** , чтобы завершить настройку Global REACH. По завершении операции вы получите подключение между двумя локальными сетями с помощью обеих каналов ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Сохранение конфигурации частного пиринга в цепи 1":::

## <a name="verify-the-configuration"></a>Проверка конфигурации

Проверьте конфигурацию Global Reach, выбрав *частный пиринг* в конфигурации канала ExpressRoute. При правильной настройке конфигурация должна выглядеть следующим образом:

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="Проверка конфигурации Global Reach":::

## <a name="disable-connectivity"></a>Отключение возможности подключения

Есть два варианта отключения Global Reach. Чтобы отключить подключение между всеми каналами, снимите флажок **включить Global REACH** , чтобы отключить подключение между всеми каналами. Чтобы отключить подключение между отдельными каналами, нажмите кнопку Удалить рядом с *именем Global REACH* , чтобы удалить связь между ними. Затем нажмите кнопку **сохранить** , чтобы завершить операцию.

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="Отключить конфигурацию Global Reach":::

После завершения операции вы больше не сможете подключиться к локальной сети через каналы ExpressRoute.

## <a name="next-steps"></a>Следующие шаги
1. [Получение дополнительных сведений об ExpressRoute Global Reach](expressroute-global-reach.md).
2. [Проверка подключения ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Подключение виртуальной сети к каналу ExpressRoute](expressroute-howto-linkvnet-arm.md).
