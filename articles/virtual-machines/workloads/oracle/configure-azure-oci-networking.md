---
title: Подключение Azure ExpressRoute с помощью облачной инфраструктуры Oracle | Документация Майкрософт
description: Подключение Azure ExpressRoute с помощью FastConnect Oracle облачной инфраструктуры (OCI) для включения нескольких облаков Oracle приложениями
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/24/2019
ms.author: rogirdh
ms.openlocfilehash: 671d7c8eb9f10e346b49056e1cc117c9882bb6e8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707574"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Настроить прямое взаимодействие между Azure и Oracle облачной инфраструктуры  

Чтобы создать [интегрированный интерфейс многооблачный](oracle-oci-overview.md) (Предварительная версия), компания Майкрософт и Oracle предоставляют прямое взаимодействие между Azure и облачной инфраструктуры Oracle (OCI) через [ExpressRoute](../../../expressroute/expressroute-introduction.md) и [ FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm). Через ExpressRoute и FastConnect взаимодействие клиенты могут оценить низкую задержку, высокую пропускную способность, закрытый прямое подключение между двумя облаками.

> [!IMPORTANT]
> Подключение между Microsoft Azure и OCI находится на этапе предварительной версии. Чтобы включить подключение с низкой задержкой между Azure и OCI, подписку Azure и аренды OCI сначала должна быть добавлена в список разрешений для этой возможности.

Ниже приведен общий обзор взаимодействия:

![Подключение нескольких облаков](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Предварительные требования

* Чтобы установить подключение между Azure и OCI, необходима Активная подписка Azure и active OCI аренды.

* Подключение можно только где расположения пиринга Azure ExpressRoute — вблизи от или в одном расположении пиринга как OCI FastConnect. См. в разделе [ограничения предварительной версии](oracle-oci-overview.md#preview-limitations).

* Подписки Azure должен быть добавлены в список разрешений для этой возможности предварительной версии. Обратитесь к представителю корпорации Майкрософт, чтобы включить эту функцию в вашей подписке.

* Свой клиент: OCI должны быть добавлены в список разрешений для этой возможности предварительной версии. Свяжитесь с представителем Oracle Дополнительные сведения.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Настройка прямого подключения между ExpressRoute и FastConnect

1. Создайте стандартный канал ExpressRoute в подписке Azure в группе ресурсов. 
    * При создании ExpressRoute, выберите **Oracle Cloud FastConnect** как поставщик услуг. Чтобы создать канал ExpressRoute, см. в разделе [Пошаговое руководство по](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    * Канал Azure ExpressRoute обеспечивает детальный полосы пропускания, а FastConnect поддерживает 1, 2, 5 или 10 Гбит/с. Таким образом рекомендуется выбрать один из этих сопоставления параметров пропускной способности в ExpressRoute.

    ![Создать канал ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Запишите в ExpressRoute **ключ службы**. Необходимо указать ключ во время настройки FastConnect канала.

    ![Ключ службы ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Вам будет выставлен счет за плата за ExpressRoute как только канал ExpressRoute подготовлен (даже если **состояние поставщика** — **не подготовлено**).

1. Выделить две закрытого пространства IP-адресов диапазонов, каждый, не пересекаются с вашей виртуальной сети Azure или сети OCI виртуальном облаке пространство IP-адресов. Мы будем называть первый диапазон IP-адресов как основной адресное пространство и второй диапазон IP-адресов как Дополнительные адресные пространства. Запишите значения параметров адреса, они понадобятся при настройке FastConnect канала.
1. Создайте шлюз с динамической маршрутизацией (DRG). Он потребуется при создании канала FastConnect. Дополнительные сведения см. в разделе [шлюз динамической маршрутизации](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) документации.
1. Создайте канал FastConnect под вашего клиента Oracle. Дополнительные сведения см. в разделе [документации Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
  
    * В разделе конфигурации FastConnect выберите **Microsoft Azure: ExpressRoute** роль поставщика.
    * Выберите шлюз динамической маршрутизации, подготовленного на предыдущем шаге.
    * Выберите пропускную способность для подготовки. Для обеспечения оптимальной производительности пропускной способности должно соответствовать пропускной способности, выбранное при создании канала ExpressRoute.
    * В **ключ поставщика службы**, вставьте ключ службы ExpressRoute.
    * Используйте первая подсеть пространство частных IP-адресов вырезано в предыдущем шаге для **основной IP-адрес BGP** и второй/30 пространство частных IP-адресов для **вторичных IP-адреса BGP** адрес.
        * Назначьте первый готовый к применению адрес из двух диапазонов IP-адрес Oracle BGP (первичная и Вторичная) и второй адрес клиенту IP-адрес BGP (с точки зрения FastConnect). Первый готовый к применению IP-адрес — это второй IP-адрес в/30 адресное пространство (первый IP-адрес зарезервирован корпорацией Майкрософт).
    * Нажмите кнопку **Создать**.
1. Выполните связывание FastConnect облачной виртуальной сети в группе клиента Oracle с помощью шлюз динамической маршрутизации, с помощью таблицы маршрутов.
1. Перейдите к Azure и убедитесь, что **состояние поставщика** для ExpressRoute канала было изменено на **инициализировано** и что пиринг типа **Azure private** был подготовить. Это является необходимым условием для выполнения следующих действий.

    ![Состояние поставщика ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Щелкните **Azure private** пиринг. Вы увидите, что сведений о пиринге автоматически будут настроены на основе данных, введенные при задании вверх FastConnect канала.

    ![Частные параметры пиринга](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Подключите виртуальную сеть для ExpressRoute

1. Создайте виртуальную сеть и шлюз виртуальной сети, если у вас еще нет. Дополнительные сведения см. в разделе [Пошаговое руководство по](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
1. Настройка подключения между шлюзом виртуальной сети и каналом ExpressRoute, выполнив [скрипт Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) или выполнив команду PowerShell, чтобы [Настройка ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

После завершения конфигурации сети, можно проверить правильность конфигурации, щелкнув **получить записи ARP** и **таблицы маршрутов Get** под колонкой пиринга ExpressRoute Private в портал Azure.

## <a name="automation"></a>Служба автоматизации

Корпорация Майкрософт создала сценарии Terraform, чтобы включить автоматическое развертывание сетевых соединений. Сценарии Terraform нужно пройти аутентификацию в Azure перед выполнением, так как они требуют соответствующих разрешений в подписке Azure. Проверка подлинности может быть выполнена с использованием [субъекта-службы Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) или с помощью Azure CLI. Дополнительные сведения см. в разделе [документации Terraform](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html).

Можно найти в этом сценарии Terraform и связанной документации, чтобы развернуть inter-connect [репозиторий GitHub](https://aka.ms/azureociinterconnecttf).

## <a name="monitoring"></a>Мониторинг

Установка агентов на обоих облаков, вы можете использовать Azure [монитор производительности сети (NPM)](../../../expressroute/how-to-npm.md) для мониторинга производительности сети end-to-end. NPM помогает легко идентифицировать проблемы с сетью и помогает устранить их.

## <a name="delete-the-interconnect-link"></a>Удаление связи соединений

Чтобы удалить соединение, следующие действия должно следовать, в указанном порядке конкретных. Невыполнение этого требования приведет к каналу ExpressRoute «в состоянии сбоя».

1. Удалите подключение ExpressRoute. Удалить соединение, нажав кнопку **удалить** значок на странице подключения. Дополнительные сведения см. в разделе [документации по ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Удалите Oracle FastConnect из консоли Oracle Cloud.
1. После удаления канала Oracle FastConnect, можно удалить канал Azure ExpressRoute.

На этом этапе завершена операция удаления и отмены провизионирования.

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о подключении, охватывающей несколько облачных между OCI и Azure см. в разделе [документации Oracle](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm).
* Используйте [сценарии Terraform](https://aka.ms/azureociinterconnecttf) для развертывания инфраструктуры для целевых приложений Oracle по Azure и настройки сетевых соединений. 