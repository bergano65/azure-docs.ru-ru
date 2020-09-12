---
title: 'ExpressRoute: фильтры маршрутов — пиринг Майкрософт: Azure CLI'
description: В этой статье описывается, как настраивать фильтры маршрутов для пиринга Майкрософт с помощью Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/07/2018
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8fbce15b84371b7b7907deff361e2a2e706bec28
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89567713"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Настройка фильтров маршрутов для пиринга Майкрософт с помощью Azure CLI

> [!div class="op_single_selector"]
> * [портале Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Фильтры маршрутов — это способ использовать подмножество поддерживаемых служб через пиринг Майкрософт. Действия, описанные в этой статье, помогут настроить фильтры маршрутов для каналов ExpressRoute и управлять ими.

Службы Microsoft 365, такие как Exchange Online, SharePoint Online и Skype для бизнеса, доступны через пиринг Майкрософт. При настройке пиринга Майкрософт в канале ExpressRoute все префиксы, которые относятся к этим службам, объявляются через установленные сеансы BGP. Значение сообщества BGP подключается к каждому префиксу для идентификации службы, предлагаемой через него. Список значений сообщества BGP и служб, с которыми они сопоставляются, см. в разделе [Поддержка сообществ BGP](expressroute-routing.md#bgp).

Если требуется подключение ко всем службам, большое число префиксов объявляется через BGP. Это значительно увеличивает размер таблиц маршрутов, которые обслуживают маршрутизаторы в вашей сети. Если вы планируете использовать только подмножество служб, предлагаемых через пиринг Майкрософт, размер таблиц маршрутизации можно уменьшить двумя способами. Можно сделать следующее:

* Отфильтровывать нежелательные префиксы путем применения фильтров маршрутов к сообществам BGP. Это стандартная сетевая практика и обычно используется во многих сетях.

* Определять фильтры маршрутов и применять их к каналу ExpressRoute. Фильтр маршрутов — это новый ресурс, который позволяет выбрать список служб, которые вы собираетесь использовать через пиринг Майкрософт. Маршрутизаторы ExpressRoute отправляют только список префиксов, которые принадлежат службам, определяемым в фильтре маршрутов.

### <a name="about-route-filters"></a><a name="about"></a>О фильтрах маршрута

При настроенном пиринге Майкрософт для канала ExpressRoute пограничные маршрутизаторы Майкрософт устанавливают пару сеансов BGP с пограничными маршрутизаторами (вашими или ваших поставщиков услуг подключения). В вашей сети маршруты не объявляются. Чтобы включить объявление маршрутов в своей сети, необходимо связать с ней фильтр маршрутов.

Фильтр маршрутов позволяет вам идентифицировать службы, которые можно использовать через пиринг Майкрософт по каналу ExpressRoute. По сути, это разрешенный список всех значений сообщества BGP. После определения ресурса фильтра маршрута и его подключения к каналу ExpressRoute все префиксы, которые сопоставляются со значениями сообщества BGP, объявляются в сети.

Чтобы иметь возможность присоединить фильтры маршрутов к службам Microsoft 365, необходимо иметь разрешение на использование служб Microsoft 365 через ExpressRoute. Если вы не имеете права использовать службы Microsoft 365 через ExpressRoute, операция присоединения фильтров маршрутов завершается ошибкой. Дополнительные сведения о процессе авторизации см. в статье [Azure ExpressRoute for Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Пиринг Майкрософт для каналов ExpressRoute, которые были настроены до 1 августа 2017 г., позволяет объявлять все префиксы служб, даже если не настроены фильтры маршрутов. Пиринг Майкрософт для каналов ExpressRoute, настроенных 1 августа 2017 г. или позднее, не будет объявлять префиксы, пока к каналу не будет присоединен фильтр маршрутов.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Рабочий процесс

Чтобы успешно подключиться к службам через пиринг Майкрософт, необходимо выполнить следующие действия по настройке:

* Необходимо иметь активный канал ExpressRoute с подготовленным пирингом Майкрософт. Для выполнения этих задач можно использовать следующие инструкции:
  * [Создайте канал ExpressRoute](howto-circuit-cli.md) и включите его на стороне поставщика услуг подключения. Канал ExpressRoute должен находиться в подготовленном и включенном состоянии.
  * [Создайте пиринг Майкрософт](howto-routing-cli.md), если вы управляете сеансом BGP напрямую или если у вашего поставщика услуг подключения подготовлен пиринг Майкрософт для вашего канала.

* Необходимо создать и настроить фильтр маршрутов.
  * Определите службы, которые вы будете использовать через пиринг Майкрософт.
  * Определите список значений сообщества BGP, связанных со службами.
  * Создайте правило, чтобы разрешить список префиксов, соответствующих значениям сообщества BGP.

* Фильтр маршрутов необходимо подключить к каналу ExpressRoute.

## <a name="before-you-begin"></a>Перед началом

Перед началом работы установите последнюю версию команд интерфейса командной строки (версию 2.0 или более позднюю). См. дополнительные сведения об [установке Azure CLI 2.0](/cli/azure/install-azure-cli) и [начале работы с Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

* Изучите [предварительные требования](expressroute-prerequisites.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.

* Вам потребуется активный канал ExpressRoute. Приступая к работе, [создайте канал ExpressRoute](howto-circuit-cli.md) ; он должен быть затем включен на стороне поставщика услуг подключения. Канал ExpressRoute должен находиться в подготовленном и включенном состоянии.

* Необходимо иметь активный пиринг Майкрософт. Следуйте инструкциям в статье [Создание и изменение канала ExpressRoute с помощью PowerShell](howto-routing-cli.md).

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Войдите в учетную запись Azure и выберите подписку.

Чтобы начать настройку, войдите в свою учетную запись Azure. Если вы воспользовались кнопкой "Попробовать", вы вошли в систему автоматически и вам не нужно подтверждать вход в учетную запись. Для подключения используйте следующие примеры:

```azurecli
az login
```

Просмотрите подписки учетной записи.

```azurecli-interactive
az account list
```

Выберите подписку, для которой требуется создать канал ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Шаг 1. Получение списка префиксов и значений сообщества BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Получение списка значений сообщества BGP

Чтобы получить список значений сообщества BGP, связанных со службами, которые доступны через пиринг Майкрософт, а также список префиксов, связанных с ними, используйте следующий командлет:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Сделайте список значений, которые вы хотите использовать

Создайте список значений сообщества BGP, которые нужно использовать в фильтре маршрута.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Шаг 2. Создание фильтра маршрутов и правила фильтрации

Фильтр может иметь только одно правило, и оно должно иметь тип "Разрешить". С этим правилом может быть связан список значений сообщества BGP.

### <a name="1-create-a-route-filter"></a>1. Создание фильтра маршрутов

Сначала создайте фильтр маршрутов. Команда `az network route-filter create` создает только ресурс фильтра маршрутов. После создания ресурса нужно создать правило и подключить его к объекту фильтра маршрута. Чтобы создать ресурс фильтра маршрута, выполните следующую команду:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. Создание правила фильтра

Чтобы создать новое правило, выполните следующую команду:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Шаг 3. Подключение фильтра маршрутов к каналу ExpressRoute

Чтобы подключить фильтр маршрутов к каналу ExpressRoute, выполните следующую команду:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Стандартные задачи

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Получение свойств фильтра маршрута

Чтобы получить свойства фильтра маршрута, выполните следующую команду.

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Обновление свойств фильтра маршрутов

Если фильтр маршрутов уже подключен к каналу, обновления в списке сообщества BGP автоматически распространяют соответствующие изменения объявлений префикса через установленные сеансы BGP. Вы можете обновить список сообщества BGP вашего фильтра маршрута с помощью следующей команды:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Отсоединение фильтра маршрутов от канала ExpressRoute

Как только фильтр маршрутов отсоединяется от канала ExpressRoute, префиксы перестают объявляться через сеанс BGP. Фильтр маршрутов можно отсоединить от канала ExpressRoute с помощью следующей команды:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Удаление фильтра маршрутов

Фильтр маршрутов можно удалить только, если он не подключен к каналу. Убедитесь, фильтр маршрутов не подключен к каналу, прежде чем пытаться удалить его. Фильтр маршрутов можно удалить, используя следующую команду:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Next Steps

Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
