---
title: Настройка пиринга между локальной средой и частным облаком
description: В этом руководстве по Решению Azure VMware показано, как создать в Решении Azure VMware пиринг ExpressRoute Global Reach с частным облаком.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: db3f5988cb8c07d9b6e80f500ac6aff8f96dfded
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750448"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Руководство по Настройка пиринга между локальной средой и частным облаком

ExpressRoute Global Reach подключает локальную среду к частным облакам. Подключение ExpressRoute Global Reach устанавливается между локальными средами и каналом ExpressRoute частного облака с имеющимся подключением ExpressRoute.  ExpressRoute Global Reach можно настроить с помощью Azure CLI и PowerShell в соответствии с предлагаемыми инструкциями. Мы дополнили описание [команд CLI](../expressroute/expressroute-howto-set-global-reach-cli.md) подробными сведениями и примерами, которые помогут вам настроить пиринг ExpressRoute Global Reach между локальными средами и частным облаком Решения Azure VMware.   

Перед включением подключения между двумя каналами ExpressRoute с помощью ExpressRoute Global Reach ознакомьтесь с документацией по [включению подключения в разных подписках Azure](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  Для канала ExpressRoute, используемого при [настройке сети между частным облаком и Azure](tutorial-configure-networking.md), необходимо создать ключи авторизации и использовать их при пиринге со шлюзами ExpressRoute или другими каналами ExpressRoute с помощью Global Reach. Вы уже использовали один ключ авторизации из канала ExpressRoute, поэтому для пиринга с каналом ExpressRoute локальной среды вам необходимо будет создать другой.

> [!TIP]
> В рамках этих инструкций считается, что канал ExpressRoute локальной среды называется _канал 1_, а канал ExpressRoute частного облака находится в другой подписке и обозначается как _канал 2_. 

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Использование имеющихся инструкций по управлению каналами ExpressRoute и пирингами ExpressRoute Global Reach
> * Создание ключа авторизации для _канала 2_ (канала ExpressRoute частного облака)
> * Использование Azure CLI в Cloud Shell в подписке _канала 1_ для включения пиринга между ExpressRoute Global Reach в локальной среде и частном облаке

## <a name="prerequisites"></a>Предварительные требования

Обязательные компоненты для работы с этим руководством:
- частное облако с каналом ExpressRoute, для которого настроен пиринг со шлюзом ExpressRoute в виртуальной сети Azure (с точки зрения процедур пиринга это _канал 2_);
- отдельный работоспособный канал ExpressRoute, используемый для подключения локальных сред к Azure (с точки зрения процедур пиринга это _канал 1_);
- [блок неперекрывающихся сетевых адресов](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) /29 для пиринга ExpressRoute Global Reach.

## <a name="create-an-expressroute-authorization-key-in-the-azure-vmware-solution-private-cloud"></a>Создание ключа авторизации ExpressRoute в частном облаке Решения Azure VMware

1. В частном облаке в разделе **Обзор**в разделе "Управление" выберите **Подключение > ExpressRoute > Request an authorization key** (Запрос ключа авторизации).

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Выберите Подключение > ExpressRoute > Запросить ключ авторизации для отправки нового запроса.":::

2. Введите имя ключа авторизации и нажмите **Создать**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Нажатие кнопки Создать для создания нового ключа авторизации":::

   После создания новый ключ появится в списке ключей авторизации для частного облака. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Проверка наличия нового ключа авторизации в списке ключей для частного облака ":::

3. Запишите ключ авторизации и идентификатор ExpressRoute вместе с блоком адресов /29. Вы будете использовать их на следующем шаге для завершения настройки пиринга. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Настройка пиринга между частным облаком и локальными средами с помощью ключа авторизации

Теперь, когда вы создали ключ авторизации для канала ExpressRoute частного облака, вы можете настроить его пиринг с каналом ExpressRoute локальной среды.  Пиринг осуществляется из канала ExpressRoute локальной среды с помощью Azure CLI в Cloud Shell с использованием идентификатора ресурса и ключа авторизации канала ExpressRoute частного облака, созданного на предыдущих шагах.

> [!TIP]  
> Для краткости выходных данных команд Azure CLI в этих инструкциях для выполнения запроса JMESPath можно использовать аргумент [`–query`, позволяющий отобразить только необходимые результаты](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).


1. Войдите на портал Azure, используя ту же подписку, к которой относится канал ExpressRoute локальный среды, и откройте Cloud Shell. В качестве оболочки оставьте Bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Вход на портал Azure и открытие Cloud Shell":::
 
2. В командной строке введите команду Azure CLI, чтобы создать пиринг, используя соответствующие сведения и идентификатор ресурса, ключ авторизации и блок сетевых адресов CIDR /29. 

   Ниже приведен пример используемой команды и выходных данных, свидетельствующих об успешности пиринга. Пример команды основан на команде, используемой на [шаге 3 раздела "Настройка подключения между каналами ExpressRoute в разных подписках Azure"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Создание пиринга ExpressRoute Global Reach с помощью команды Azure CLI в Cloud Shell":::
 
   Теперь вы сможете подключаться из локальных сред к частному облаку при помощи пиринга ExpressRoute Global Reach.

> [!TIP]
> Вы можете удалить только что созданный пиринг, выполнив инструкции в разделе [Отключение подключения между локальными сетями](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks).


## <a name="next-steps"></a>Дальнейшие действия

Если вы планируете применить расширение гибридного облака (HCX) для переноса рабочих нагрузок виртуальных машин в частное облако, воспользуйтесь процедурой [установки HCX для решения Azure VMware](hybrid-cloud-extension-installation.md).


<!-- LINKS - external-->

<!-- LINKS - internal -->
