---
title: Руководство. Создание и изменение канала с помощью ExpressRoute
description: В этом учебнике приведены сведения о создании, подготовке, проверке, обновлении, удалении и отзыве канала ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: duau
ms.openlocfilehash: 58c35b094d21dc562e61b4819c0d8e063908392d
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322147"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Руководство по Создание и изменение канала ExpressRoute

> [!div class="op_single_selector"]
> * [Портал Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Шаблон Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Видео — портал Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (классическая модель)](expressroute-howto-circuit-classic.md)
>

В этом учебнике показано, как создать цепь Azure ExpressRoute, используя портал Azure и модель развертывания с помощью Azure Resource Manager. Вы также сможете проверять состояние каналов, обновлять, удалять или отзывать их.

Из этого руководства вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание канала ExpressRoute
> * Получение текущего состояния цепи
> * Изменение канала
> * Отзыв и удаление канала

## <a name="before-you-begin"></a>Перед началом

* Изучите [предварительные требования](expressroute-prerequisites.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.
* Убедитесь в том, что у вас есть доступ к [порталу Azure](https://portal.azure.com).
* Убедитесь в том, что у вас есть разрешения на создание сетевых ресурсов. Если у вас нет нужных разрешений, обратитесь к администратору учетной записи.
* Вы можете [просмотреть видео](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit), прежде чем начать, чтобы лучше понять выполняемые действия.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Создание и предоставление канала ExpressRoute

### <a name="1-sign-in-to-the-azure-portal"></a>1. Вход на портал Azure

В браузере откройте [портал Azure](https://portal.azure.com) и выполните вход с помощью учетной записи Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Создание канала ExpressRoute

> [!IMPORTANT]
> Выставление счетов за использование ExpressRoute начинается после получения ключа службы. Обязательно выполните эту операцию, как только поставщик услуг подключения будет готов предоставить канал.

Чтобы создать канал ExpressRoute, можно выбрать команду создания ресурса. 

1. В меню портала Azure выберите **Создать ресурс**. Выберите **Сети** > **ExpressRoute**, как показано на рисунке ниже.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="Создание канала ExpressRoute":::

2. Когда вы щелкнете **ExpressRoute**, отобразится страница **Create ExpressRoute** (Создание ExpressRoute). Укажите значения для параметров **Группа ресурсов**, **Регион** и **Имя** для канала. Выберите **Next: Конфигурация >** .

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Настройка группы ресурсов и региона":::

3. При заполнении значений на этой странице вам нужно указать правильный уровень SKU ("Локальный", "Стандартный" или "Премиум") и модель выставления счетов на основе измерения данных ("С учетом трафика" или "Не ограничено").

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Настройка цепи":::
    
    * **Тип порта** определяет, подключаетесь ли вы к поставщику услуг или непосредственно в глобальную сеть Майкрософт в расположении пиринга.
    * **Создать или импортировать из классической версии** определяет, создается ли цепь или выполняется перенос классической цепи в ARM.
    * **Поставщик** — это поставщик услуг Интернета, через которого вы будете запрашивать службу.
    * **Расположение пиринга** — физическое расположение пиринга с корпорацией Майкрософт.

    > [!IMPORTANT]
    > Параметр "Расположение пиринга" определяет [физическое расположение](expressroute-locations.md) пиринга с корпорацией Майкрософт. Оно **не** связано со свойством Location, которое ссылается на географический регион, в котором находится поставщик сетевых ресурсов Azure. Хотя они не связаны, рекомендуется выбрать поставщик сетевых ресурсов, находящийся недалеко от расположения пиринга канала.

    * **Инвентаризационный номер (SKU)** определяет, какая надстройка включена — ExpressRoute уровня "Локальный", ExpressRoute уровня "Стандартный" или ExpressRoute уровня "Премиум". Укажите **Локальный**, чтобы получить SKU "Локальный", **Стандартный** для получения SKU "Стандартный" или **Премиум**, чтобы получить надстройку "Премиум".
    * **Модель выставления счетов** определяет тип выставления счетов. Выберите **С учетом трафика** для тарифного плана с оплатой за трафик или **Не ограничено** для безлимитного тарифного плана. Обратите внимание, что тарифный план **С учетом трафика** можно изменить на **Не ограничено**.

    > [!IMPORTANT]
    > Тарифный план **Не ограничено** нельзя изменить на план **С учетом трафика**.

    * **Allow classic operation** (Разрешить классические операции) позволит связать классические виртуальные сети с цепью.

### <a name="3-view-the-circuits-and-properties"></a>3. Просмотр каналов и свойств

**Просмотр всех каналов**

Вы можете просмотреть все созданные вами каналы, последовательно выбрав в меню слева пункты **Все службы > Сетевые подключения > Цепи ExpressRoute**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Меню цепи Expressroute":::

Здесь будут отображаться все цепи Expressroute, созданные в подписке.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Список цепей Expressroute":::

**Просмотр свойств**

Чтобы просмотреть свойства канала, выберите его. На странице **Обзор** канала отобразится ключ службы в соответствующем поле. Просмотрите ключ службы своей цепи и предоставьте его поставщику услуг для завершения подготовки. Ключ службы соответствует каналу.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Просмотр свойств":::

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Отправка ключа службы поставщику услуг подключения для подготовки

На этой странице параметр **Состояние поставщика** предоставляет сведения о текущем состоянии подготовки на стороне поставщика услуг. Параметр **Состояние цепи** предоставляет состояние на стороне инфраструктуры Майкрософт. Дополнительные сведения о состояниях подготовки канала см. в статье [Рабочие процессы](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Вновь созданный канал ExpressRoute будет имеет следующее состояние:

Состояние поставщика: **Не подготовлено**<BR>
Состояние канала: **Enabled**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Инициация процесса подготовки":::

Когда поставщик услуг подключения находится в процессе его включения, канал переходит в следующее состояние:

Состояние поставщика: **Подготовка**<BR>
Состояние канала: **Enabled**

Для того чтобы канал ExpressRoute можно было использовать, он должен находиться в следующем состоянии:

Состояние поставщика: **Подготовлено**<BR>
Состояние канала: **Enabled**

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Периодическая проверка состояния и статуса ключа канала

Чтобы просмотреть свойства интересующего вас канала, выберите его. Проверьте **Состояние поставщика**. Чтобы вы могли продолжить работу, оно должно измениться на **Подготовлено**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Состояние канала и поставщика":::

### <a name="6-create-your-routing-configuration"></a>6. Создание конфигурации маршрутизации

Пошаговые инструкции по созданию и изменению пиринга каналов см. в статье [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-portal-resource-manager.md).

> [!IMPORTANT]
> Эти инструкции распространяются только на каналы от поставщиков, предоставляющих услуги подключения второго уровня. Если ваш поставщик услуг подключения предлагает услуги третьего уровня (обычно это IPVPN, например MPLS), то он возьмет на себя настройку маршрутизации и управление ею.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Связывание виртуальной сети с каналом ExpressRoute

Теперь свяжите виртуальную сеть с каналом ExpressRoute. При работе с моделью развертывания Resource Manager пользуйтесь статьей [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md) .

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Получение состояния канала ExpressRoute

Чтобы просмотреть состояние канала, выберите его и просмотрите данные на странице "Обзор".

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Изменение канала ExpressRoute

Некоторые свойства канала ExpressRoute можно изменить, не повлияв на подключение. На странице **Конфигурация** можно изменить пропускную способность, номер SKU, модель выставления счетов, а также разрешить классические операции. Сведения об ограничениях см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

Вы можете выполнять следующие задачи без простоя:

* Включать и отключать надстройку ExpressRoute "Премиум" для канала ExpressRoute.
* Увеличивать пропускную способность канала ExpressRoute при условии, что в порту имеется доступная емкость.

  > [!IMPORTANT]
  > Снижение уровня пропускной способности канала не поддерживается.

* Переходить с тарифного плана *с оплатой за трафик* на *безлимитный тарифный план*.

  > [!IMPORTANT]
  > Переход с **безлимитного тарифного плана** на **тарифный план с оплатой за трафик** не поддерживается.

* Параметр *Allow Classic Operations*(Разрешить классические операции) можно включать и отключать.
  > [!IMPORTANT]
  > Может потребоваться заново создать канал ExpressRoute, если существующий порт не обеспечивает достаточную емкость. Канал невозможно обновить, если в его расположении нет доступной дополнительной емкости.
  >
  > Вы можете легко обновить пропускную способность, но уменьшить пропускную способность канала ExpressRoute без прерывания его работы нельзя. Для снижения пропускной способности нужно будет отозвать канал ExpressRoute и повторно подготовить новый канал ExpressRoute.
  >
  > Отключение надстройки уровня "Премиум" может завершиться ошибкой, если используется больше ресурсов, чем разрешено для канала уровня "Стандартный".

Чтобы изменить канал ExpressRoute, щелкните **Конфигурация**.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Изменение канала":::

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>отзыве и удалении канала ExpressRoute

Если подготовка поставщика услуг канала ExpressRoute находится в состоянии **Идет подготовка** или **Подготовлено** то свяжитесь с поставщиком услуг, чтобы отозвать канал с его стороны. Мы будем резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.

> [!NOTE]
>* Прежде чем инициировать отзыв, необходимо разорвать связь между цепью ExpressRoute и *всеми виртуальными сетями*. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.
>* Если поставщик услуг отзовет канал (состояние подготовки поставщика услуг изменится на **Не подготовлено**), вы можете удалить такой канал. Это приостанавливает выставление счетов для канала.

Цепь ExpressRoute можно удалить, щелкнув **соответствующий** значок. 

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Удаление цепи":::

## <a name="next-steps"></a>Дальнейшие действия

Создав канал, выполните задачи, описанные в следующих статьях:

* [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md)