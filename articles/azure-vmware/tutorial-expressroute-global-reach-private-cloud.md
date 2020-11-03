---
title: Учебник по настройке пиринга между локальной средой и частным облаком
description: Создание пиринга ExpressRoute Global Reach к частному облаку в решении Azure VMware.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 4d10972a693f7c4c3ae25a5bc986f6c15e978294
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912503"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Руководство по Настройка пиринга между локальной средой и частным облаком

ExpressRoute Global Reach подключает локальную среду к частному облаку решения Azure VMware. Подключение ExpressRoute Global Reach устанавливается между локальными средами и каналом ExpressRoute частного облака с имеющимся подключением ExpressRoute. 

Для канала ExpressRoute, используемого при [настройке сети между частным облаком и Azure](tutorial-configure-networking.md), необходимо создать и использовать ключи авторизации.  Вы уже использовали один ключ авторизации из канала ExpressRoute, поэтому в этом учебнике для пиринга с каналом ExpressRoute локальной среды вам необходимо будет создать другой.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Создание второго ключа авторизации для _канала 2_ (канала ExpressRoute частного облака)
> * Использование [портала Azure](#azure-portal-method) или [Azure CLI в Cloud Shell](#azure-cli-in-a-cloud-shell-method) в подписке _канала 1_ для включения пиринга между ExpressRoute Global Reach в локальной среде и частном облаке


## <a name="before-you-begin"></a>Перед началом

Перед включением подключения между двумя каналами ExpressRoute с помощью ExpressRoute Global Reach ознакомьтесь с документацией по [включению подключения в разных подписках Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Предварительные требования

- Установленное подключение к частному облаку решения Azure VMware и из него с помощью канала ExpressRoute, подключенного к шлюзу ExpressRoute в виртуальной сети Azure (VNet) — _канал 2_ из процедур пиринга.  
- Отдельный работоспособный канал ExpressRoute, используемый для подключения локальных сред к Azure — _канал 1_ с точки зрения процедур пиринга.
- [блок неперекрывающихся сетевых адресов](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) /29 для пиринга ExpressRoute Global Reach.

> [!TIP]
> В рамках этих предварительных требований считается, что канал ExpressRoute локальной среды называется _канал 1_ , а канал ExpressRoute частного облака находится в другой подписке и обозначается как _канал 2_. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>Создание ключа авторизации ExpressRoute в частном облаке

1. В частном облаке в разделе **Обзор** в разделе "Управление" выберите **Подключение > ExpressRoute > Request an authorization key** (Запрос ключа авторизации).

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Выберите &quot;Подключение&quot; > &quot;ExpressRoute&quot; > &quot;Запросить ключ авторизации&quot; для отправки нового запроса.":::

2. Введите имя ключа авторизации и нажмите **Создать**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Выбор кнопки &quot;Создать&quot; для создания нового ключа авторизации":::

   После создания новый ключ появится в списке ключей авторизации для частного облака. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Проверка наличия нового ключа авторизации в списке ключей для частного облака ":::

3. Запишите ключ авторизации и идентификатор ExpressRoute вместе с блоком адресов /29. Вы будете использовать их на следующем шаге для завершения настройки пиринга. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Настройка пиринга между частным облаком и локальными средами с помощью ключа авторизации

Теперь, когда вы создали ключ авторизации для канала ExpressRoute частного облака, вы можете настроить его пиринг с каналом ExpressRoute локальной среды.  С точки зрения локального канала ExpressRoute пиринг выполняется на [портале Azure](#azure-portal-method) или с помощью [Azure CLI в Cloud Shell](#azure-cli-in-a-cloud-shell-method). Оба этих метода предусматривают использование идентификатора ресурса и ключа авторизации для канала ExpressRoute частного облака, чтобы завершить пиринг.

### <a name="azure-portal-method"></a>Метод портала Azure

1. Войдите на [портал Azure](https://portal.azure.com), используя ту же подписку, к которой относится канал ExpressRoute локальный среды.

1. На странице **Обзор** частного облака в разделе "Управление" выберите **Подключение > ExpressRoute Global Reach > Добавить**.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="В меню выберите &quot;Подключение&quot;, перейдите на вкладку &quot;ExpressRoute Global Reach&quot;, а затем выберите &quot;Добавить&quot;.":::

1. Вы можете создать локальное облачное подключение, выбрав один из следующих вариантов.

   - Выберите в списке канал ExpressRoute.
   - Если у вас есть идентификатор канала, скопируйте и вставьте его.

1. Выберите **Подключиться**. Новое подключение отобразится в списке локальных облачных подключений.  

>[!TIP]
>Вы можете удалить или отключить подключение в списке, выбрав элемент **Еще**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Отключение или удаление локального подключения":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Метод Azure CLI в Cloud Shell

[Команды CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) дополнены подробными сведениями и примерами, которые помогут настроить пиринг ExpressRoute Global Reach между локальными средами и частным облаком Решения Azure VMware.  

> [!TIP]  
> Для краткости выходных данных команд Azure CLI в этих инструкциях для выполнения запроса JMESPath можно использовать аргумент [`–query`, позволяющий отобразить только необходимые результаты](/cli/azure/query-azure-cli).


1. Войдите на портал Azure, используя ту же подписку, к которой относится канал ExpressRoute локальный среды, и откройте Cloud Shell. В качестве оболочки оставьте Bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Вход на портал Azure и открытие Cloud Shell":::
 
2. Введите команду Azure CLI, чтобы создать пиринг. Используйте конкретные сведения и идентификатор ресурса, ключ авторизации и сетевой блок CIDR /29. 

   На изображении приведен пример используемой команды и выходных данных, свидетельствующих об успешности пиринга. Пример команды основан на команде, используемой на [шаге 3 раздела "Настройка подключения между каналами ExpressRoute в разных подписках Azure"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Создание пиринга ExpressRoute Global Reach с помощью команды Azure CLI в Cloud Shell":::
 
   Теперь можно подключаться из локальных сред к частному облаку при помощи пиринга ExpressRoute Global Reach.

> [!TIP]
> Вы можете удалить только что созданный пиринг, выполнив инструкции в разделе [Отключение подключения между локальными сетями](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks).


## <a name="next-steps"></a>Дальнейшие действия

Из этого учебника вы узнали, как создать второй ключ авторизации для канала ExpressRoute частного облака. Вы также узнали, как включить пиринг ExpressRoute Global Reach подключения из локальной среды к частному облаку. 

Перейдите к следующему руководству, чтобы узнать, как развернуть и настроить решение VMware HCX для частного облака решения Azure VMware.

> [!div class="nextstepaction"]
> [Развертывание и настройка VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->