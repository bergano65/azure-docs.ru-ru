---
title: Подключите Azure ExpressRoute с облачной инфраструктурой Oracle (ru) Документы Майкрософт
description: Подключите Azure ExpressRoute с помощью Oracle Cloud Infrastructure (OCI) FastConnect для включения кросс-облачных решений приложений Oracle
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2020
ms.author: borisb
ms.openlocfilehash: 70556cbbfefd6ad22ef96ee16065209031ea456c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683754"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Настройка прямого соединения между Azure и облачной инфраструктурой Oracle  

Для создания [интегрированного мультиобуладора](oracle-oci-overview.md)Microsoft и Oracle предлагают прямую взаимосвязь между Azure и облачной инфраструктурой Oracle (OCI) через [ExpressRoute](../../../expressroute/expressroute-introduction.md) и [FastConnect.](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) Благодаря взаимодействию ExpressRoute и FastConnect клиенты могут испытывать низкую задержку, высокую пропускную связь, частную прямую связь между двумя облаками.

> [!IMPORTANT]
> Oracle сертифицирует эти приложения для запуска в Azure при использовании решения для интерконнекта Azure /Oracle Cloud к маю 2020 года.
> * E-Бизнес Люкс
> * JD Эдвардс EnterpriseOne
> * Peoplesoft
> * Приложения Oracle Retail
> * Oracle Гиперион Финансовое управление

Следующее изображение показывает обзор взаимосвязи на высоком уровне:

![Кросс-облачное сетевое соединение](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>Предварительные требования

* Чтобы установить связь между Azure и OCI, необходимо иметь активную подписку Azure и активную аренду OCI.

* Связь возможна только в том случае, если место пиринга Azure ExpressRoute находится в непосредственной близости от или в том же месте, что и OCI FastConnect. См [Доступность региона](oracle-oci-overview.md#region-availability).

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Налаживание прямого соединения между ExpressRoute и FastConnect

1. Создайте стандартную схему ExpressRoute в подписке Azure под группой ресурсов. 
    * Создавая ExpressRoute, выберите **Oracle Cloud FastConnect** в качестве поставщика услуг. Чтобы создать схему ExpressRoute, см. [step-by-step guide](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)
    * Схема Azure ExpressRoute предоставляет возможности гранулированной пропускной способности, в то время как FastConnect поддерживает 1, 2, 5 или 10 Гбит/с. Поэтому рекомендуется выбрать один из этих вариантов соответствия пропускной способности в ExpressRoute.

    ![Создание схемы ExpressRoute](media/configure-azure-oci-networking/exr-create-new.png)
1. Запишите ключ **службы**ExpressRoute. При настройке трассы FastConnect необходимо предоставить ключ.

    ![Ключ службы ExpressRoute](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > Вам будет выставлен счет за экспрессRoute, как только схема ExpressRoute будет подготовлена (даже если **статус поставщика** **не предусмотрен).**

1. Вырезать два частных IP-адреса пространства /30 каждый, которые не пересекаются с вашей виртуальной сети Azure или OCI виртуальной облачной сети IP-адрес пространстве. Мы будем называть первое ip-адресное пространство основным адресное пространство, а второе ip-адресное пространство — второстепенным адресное пространство. Запишите адреса, которые вам нужны при настройке схемы FastConnect.
1. Создайте динамический шлюз для routing (DRG). Это понадобится при создании схемы FastConnect. Для получения дополнительной информации ознакомьтесь с документацией [Dynamic Routing Gateway.](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)
1. Создайте схему FastConnect под арендатором Oracle. Для получения дополнительной [информации](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)см.
  
    * В рамках конфигурации FastConnect выберите в качестве поставщика **Microsoft Azure: ExpressRoute.**
    * Выберите динамический шлюз, который вы приковали на предыдущем этапе.
    * Выберите пропускную способность, которая будет подготовлена. Для оптимальной производительности пропускная способность должна соответствовать выбранной пропускной способности при создании схемы ExpressRoute.
    * В **ключе обслуживания поставщика**, вставьте ключ обслуживания ExpressRoute.
    * Используйте первое пространство для частных IP-адресов/30, вырезанное на предыдущем этапе для **IP-адреса Primary BGP,** и второе место для частного IP-адреса /30 для **вторичного IP-адреса BGP.**
        * Назначить первый адрес двух диапазонов для IP-адреса Oracle BGP (первичный и вторичный) и второй адрес для IP-адреса клиента BGP (с точки зрения FastConnect). Первый использоваемый IP-адрес — второй IP-адрес в адресном пространстве /30 (первый IP-адрес зарезервирован корпорацией Майкрософт).
    * Нажмите кнопку **Создать**.
1. Полная связь FastConnect с виртуальной облачной сетью под вашим клиентом Oracle через динамический шлюз маршрутизаний, используя Route Table.
1. Перейдите в Azure и убедитесь, что **статус поставщика** для вашей схемы ExpressRoute был изменен на **Provisioned** и что был подготовлен приранженый тип **Azure private.** Это предварительная необходимость для следующих шагов.

    ![Статус поставщика ExpressRoute](media/configure-azure-oci-networking/exr-provider-status.png)
1. Нажмите на приватное пиринг **Azure.** Вы увидите, что детали вpeering автоматически настроены на основе информации, введенной при настройке схемы FastConnect.

    ![Настройки приватного пиринга](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Подключение виртуальной сети к ExpressRoute

1. Создайте виртуальную сеть и виртуальный сетевой шлюз, если вы еще не сделали. Для получения подробной информации смотрите [пошаговое руководство.](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
1. Навлажьте соединение между виртуальным сетевым шлюзом и схемой ExpressRoute, выявив [сценарий Terraform](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) или выдвинув команду PowerShell для [настройки ExpressRoute FastPath.](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)

После завершения конфигурации сети можно проверить достоверность конфигурации, нажав на **Get ARP Records** и **получить таблицу маршрутов** под лезвием приватного лезвия ExpressRoute Private на портале Azure.

## <a name="automation"></a>Служба автоматизации

Корпорация Майкрософт создала скрипты Terraform для автоматического развертывания сетевого интерсоединения. Скрипты Terraform должны быть проверены с помощью Azure перед выполнением, поскольку для этого требуются соответствующие разрешения на подписку Azure. Аутентификация может быть выполнена с помощью [основного сервиса Azure Active Directory](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) или с помощью Azure CLI. Для получения дополнительной информации [см.](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)

Скрипты Terraform и связанная с ними документация для развертывания взаимосвязей можно найти в этом [репозитории GitHub.](https://aka.ms/azureociinterconnecttf)

## <a name="monitoring"></a>Наблюдение

Устанавливая агенты в обоих облаках, можно использовать Azure [Network Performance Monitor (NPM)](../../../expressroute/how-to-npm.md) для мониторинга производительности сквозной сети. NPM помогает легко выявлять сетевые проблемы и устранять их.

## <a name="delete-the-interconnect-link"></a>Удалить ссылку на взаимосвязанные соединения

Чтобы удалить интерконнект, следующие шаги должны быть соблюдены, в конкретном порядке. Невыполнение этого требования приведет к "неудавому состоянию" схемы ExpressRoute.

1. Удалить соединение ExpressRoute. Удалите соединение, нажав значок **Удалить** на странице для подключения. Для получения дополнительной информации смотрите [документацию ExpressRoute](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet).
1. Удалите Oracle FastConnect с облачной консоли Oracle.
1. После удаления схемы Oracle FastConnect можно удалить схему Azure ExpressRoute.

На этом этапе процесс удаления и деобеспечения завершен.

## <a name="next-steps"></a>Следующие шаги

* Для получения дополнительной информации о кросс-облачной связи [Oracle documentation](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)между OCI и Azure см.
* Используйте [скрипты Terraform](https://aka.ms/azureociinterconnecttf) для развертывания инфраструктуры для целевых приложений Oracle через Azure и настройки сетевого интерсоединения. 
