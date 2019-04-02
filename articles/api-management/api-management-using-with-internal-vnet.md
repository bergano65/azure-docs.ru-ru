---
title: Как использовать управление API Azure с внутренними виртуальными сетями | Документация Майкрософт
description: Узнайте, как установить и настроить управление API Azure для внутренней виртуальной сети
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: da27c772a0650a923068b3c519ef39494573f96a
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793124"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Использование службы управления API Azure совместно с внутренней виртуальной сетью
Служба управления API Azure в сочетании с виртуальными сетями Azure позволяет работать с интерфейсами API, которые недоступны из Интернета. Подключение можно создать с применением разных технологий VPN. Управление API можно развернуть в виртуальной сети в одном из двух основных режимов:
* Внешний
* Внутренний

Если управление API развернуто во внутреннем режиме виртуальной сети, все конечные точки службы (шлюз, портал разработчика, портал Azure, конечная точка прямого управления и репозиторий Git) доступны только из этой виртуальной сети и вы контролируете доступ к ней. Ни одна из конечных точек служб не регистрируется на общедоступных DNS-серверах.

Используя управление API во внутреннем режиме, вы можете реализовать следующие сценарии:

* предоставлять третьим лицам защищенный доступ к API, размещенным в частном центре обработки данных, с помощью VPN-подключений типа "сеть — сеть" или Azure ExpressRoute;
* создать гибридное облачное развертывание, предоставляя единый шлюз для облачных и локально размещенных API-интерфейсов;
* управлять API-интерфейсами, размещенными в нескольких географических расположениях, через одну общую конечную точку шлюза.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Технические условия

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

+ **Активная подписка Azure.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Экземпляр Azure API Management.** Дополнительные сведения см. в статье о [создании экземпляра управления API Azure](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Создание управления API во внутренней виртуальной сети
Служба управления API во внутренней виртуальной сети размещается за [внутренней подсистемы балансировки нагрузки (Классическая модель)](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Это является единственным доступным параметром и не может быть изменено.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Настройка подключения к виртуальной сети с помощью портала Azure

1. Перейдите к экземпляру управления API Azure на [портале Azure](https://portal.azure.com/).
2. Щелкните **Виртуальная сеть**.
3. Укажите, что развертывание экземпляра управления API выполняется внутри виртуальной сети.

    ![Меню для настройки управления API Azure во внутренней виртуальной сети][api-management-using-internal-vnet-menu]

4. Щелкните **Сохранить**.

После успешного завершения развертывания вы увидите **частного** виртуальный IP-адрес и **открытый** виртуальный IP-адрес службы управления API в колонке "Обзор". **Частного** виртуальный IP-адрес является нагрузки Сбалансированный IP-адрес из управления API делегированные подсети, по которому `gateway`, `portal`, `management` и `scm` доступных конечных точек. **Открытый** виртуальный IP-адрес используется **только** трафик плоскости управления `management` конечной точки отказа порт 3443 и могут быть заблокированы до [ApiManagement] [ ServiceTags] servicetag.

![Панель мониторинга службы управления API, демонстрирующая настроенную внутреннюю виртуальную сеть][api-management-internal-vnet-dashboard]

> [!NOTE]
> Консоль тестирования, доступная на портале Azure, не будет работать для **внутренней** развернутой службы виртуальной сети, так как URL-адрес шлюза не зарегистрирован в общедоступной службе DNS. Вместо этого следует использовать консоль тестирования, доступную на **портале разработчика**.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Настройка подключения к виртуальной сети с помощью командлетов PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Вы можете настроить подключение к виртуальной сети с помощью командлетов PowerShell.

* Создание службы управления API в виртуальной сети: Используйте командлет [New AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) создайте службу управления API Azure в виртуальной сети и настроить его для использования типа внутренней виртуальной сети.

* Обновление существующего развертывания службы управления API в виртуальной сети: Используйте командлет [AzApiManagementRegion обновления](/powershell/module/az.apimanagement/update-azapimanagementregion) переместить существующую службу управления API в виртуальной сети и настроить его для использования типа внутренней виртуальной сети.

## <a name="apim-dns-configuration"></a>Настройка DNS
Если управление API настроено во внешнем режиме виртуальной сети, службой DNS управляет Azure. Чтобы использовать внутренний режим виртуальной сети, управлять маршрутизацией вы должны самостоятельно.

> [!NOTE]
> Служба управления API не прослушивает запросы, поступающие на IP-адреса. Она реагирует только на запросы к имени узла, которое настроено на конечных точках службы. К ним относятся шлюз, портал Azure и портал разработчика, конечная точка прямого управления и репозиторий Git.

### <a name="access-on-default-host-names"></a>Доступ по именам узлов по умолчанию
При создании службы управления API, например, с именем «contosointernalvnet» по умолчанию настроены следующие конечные точки службы:

   * Шлюз или прокси-сервера: contosointernalvnet.azure-api.net.

   * На портале Azure и портала разработчика: contosointernalvnet.portal.azure-api.net.

   * Конечная точка прямого управления: contosointernalvnet.management.azure-api.net.

   * Git: contosointernalvnet.scm.azure-api.net.

Чтобы получить доступ к этим конечным точкам службы управления API, можно создать виртуальную машину в подсети, подключенной к виртуальной сети, в которой развернута служба управления API. Если внутренний виртуальный IP-адрес для службы является 10.1.0.5, можно сопоставить файл hosts, % SystemDrive%\drivers\etc\hosts, следующим образом:

   * 10.1.0.5     contosointernalvnet.azure-api.net

   * 10.1.0.5     contosointernalvnet.portal.azure-api.net

   * 10.1.0.5     contosointernalvnet.management.azure-api.net

   * 10.1.0.5     contosointernalvnet.scm.azure-api.net

Теперь вы сможете обращаться с этой виртуальной машины к любой из конечных точек службы.
Если в вашей в виртуальной сети действует пользовательский DNS-сервер, вы можете создать на нем записи DNS типа A. Тогда доступ к конечным точкам будет возможен из любого расположения в пределах виртуальной сети.

### <a name="access-on-custom-domain-names"></a>Доступ по пользовательским доменным именам

1. Если для обращения к службе управления API вы не хотите использовать имена узлов по умолчанию, для всех конечных точек службы можно настроить пользовательские доменные имена, как указано ниже:

   ![Настройка пользовательского домена для управления API][api-management-custom-domain-name]

2. После этого создайте на DNS-сервере записи типа A для обращения к конечным точкам, которые будут доступны только в пределах виртуальной сети.

## <a name="routing"> </a> Маршрутизация
+ Частный виртуальный IP-адрес для подсистемы балансировки нагрузки из диапазона подсети резервируется и используется для доступа к конечным точкам службы управления API из виртуальной сети.
+ Чтобы обеспечить доступ ко всем конечным точкам службы управления только через порт 3443, также резервируется общедоступный IP-адрес для подсистемы балансировки нагрузки.
+ Для доступа к ресурсам в пределах виртуальной сети используется выделенный IP-адрес из диапазона подсети, а за пределами этой сети — общедоступный виртуальный IP-адрес.
+ Общедоступный и частный IP-адреса для подсистемы балансировки нагрузки можно узнать в колонке Overview/Essentials (Обзор и основные компоненты) на портале Azure.

## <a name="related-content"> </a>Связанная информация
Для получения дополнительных сведений ознакомьтесь со следующими статьями:
* [Распространенные проблемы конфигурации сети][Common network configuration problems] при настройке управления API Azure с виртуальными сетями
* [Виртуальная сеть Azure: часто задаваемые вопросы](../virtual-network/virtual-networks-faq.md)
* Инструкции по [созданию записей DNS типа A](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

