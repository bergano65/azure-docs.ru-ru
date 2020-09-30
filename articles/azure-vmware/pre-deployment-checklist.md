---
title: Контрольный список предварительного развертывания решения Azure VMware
description: Используйте этот контрольный список в рамках процесса планирования перед развертыванием.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 51480f72d6de6b1f49aa753e006c4683bbd48cc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580025"
---
# <a name="azure-vmware-solution-pre-deployment-checklist"></a>Контрольный список предварительного развертывания решения Azure VMware
Вы будете использовать этот контрольный список перед развертыванием на [этапе планирования](production-ready-deployment-steps.md).

## <a name="success-criteria"></a>Критерии успешного завершения
Определите, какие тесты вы собираетесь выполнять, и ожидаемый результат.

| Требуются фундаментальные сведения | Ваш ответ |
| ----------- | ------------- |
| Развертывание SDDC | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Создание виртуальной сети | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Создать поле перехода | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Создание шлюза виртуальной сети | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Подключение ExpressRoute Global Reach | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Вход в НСКС Manager и vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Требуются основные сведения | Ваш ответ |
| --------| --------------|
| Создание DHCP-сервера | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Создание сегментов сети | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Масштабирование (Добавление или удаление узлов) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Развертывание VMware ХККС | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Создание виртуальных машин | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
| Включить Интернет | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Миграция виртуальной машины из локальной среды в частное облако | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Требуются дополнительные сведения | Ваш ответ |
| --------| --------------|
| Операции с моментальными снимками ВМ | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Развертывание подсистемы балансировки нагрузки НСКС-T | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Интеграция с Azure<br><ul><li>Общая библиотека содержимого</li><li>Интеграция решений для обеспечения безопасности</li><li>Отправить ISO-файл</li><li>Сборка из ISO</li><li>Azure Backup</li></ul> | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Микросегментация | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Маршрутизация | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |




## <a name="azure-vmware-solution-information"></a>Сведения о решении Azure VMware

#### <a name="azure-subscription"></a>Подписка Azure.
Укажите имя подписки и идентификатор подписки для решения VMware для Azure. Подписка может быть новой или существующей. Не используйте подписку на разработку и тестирование.

| Необходимая информация  | Ваш ответ |
| ------------| ------------- |
| Подписка и идентификатор | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Подписка с соглашением EA | ☐ Да &nbsp; &nbsp; ☐ нет  |
| Имя группы ресурсов | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Расположение | ☒ Восточная часть США |
| Администратор Azure<br><br>Указание имени и контакта администратора<br>назначено, чтобы включить службу из Marketplace.<br>Участник является минимальной ролью, необходимой для <br>[зарегистрируйте поставщик ресурсов решения Azure VMware](tutorial-create-private-cloud.md#register-the-resource-provider). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="on-premises-vmware-information"></a>Сведения о локальной среде VMware

| Необходимая информация  | Ваш ответ |
| ------------| --------------|
| Версия vSphere | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Версия vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Администратор vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Расширение L2<br><br>При расширении сетей L2 с помощью VMware ХККС<br>Укажите локальную сеть, которую вы будете расширять. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vSwitch<br><br>Укажите тип vSwitch, используемый во всей среде. | ☐ Стандартный &nbsp; &nbsp; ☐ распределен<br><br>Если используется Standard, то VMware ХККС недоступен. |
| DNS и DHCP<br><br>Требуется правильная инфраструктура DNS и DHCP. <br>Рекомендуется использовать службу DHCP, встроенную в <br>НСКС или используйте локальный DHCP-сервер в частном облаке <br>вместо маршрутизации широковещательного трафика DHCP по адресу <br>Локальная Глобальная сеть. Дополнительные сведения см.: <br>Узнайте, [как создавать DHCP и управлять ими в решении Azure VMware](manage-dhcp.md). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---azure-vmware-solution-infrastructure"></a>Сети — инфраструктура решений VMware для Azure 
Необходимые данные помогут вам удовлетворить требования к сети для решения Azure VMware, чтобы обеспечить поддержку и начальное тестирование сети. 

| Необходимая информация | Ваш ответ |
| ----------- | ------------- |
|Azure VMware решение CIDR<br><br>Требуется для узлов vSphere, vSAN и управления <br>сети в решении VMware для Azure. Дополнительные сведения <br>сведения см. в разделе [рекомендации по маршрутизации и подсети](tutorial-network-checklist.md#routing-and-subnet-considerations). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Служба Azure VMware для рабочих нагрузок CIDR (необязательно)<br><br>Назначение сети для использования в Azure VMware<br>Решение для первоначального тестирования. Виртуальные машины<br>будет развернут для проверки сетевого подключения <br>из/в решение VMware для Azure. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---connect-azure-vmware-solution-to-azure-virtual-network"></a>Сеть. Подключение решения Azure VMware к виртуальной сети Azure
Данные, необходимые для работы кластера решений VMware для Azure, можно подключить к Azure через канал ExpressRoute, который входит в состав службы решения Azure VMware.

| Необходимая информация | Ваш ответ |
| ------------------ | ------------- |
| Поле перехода — Windows Server | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Создание виртуальной сети | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Создание подсети шлюза | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Создание шлюза виртуальной сети<br><br>Дополнительные сведения см. [в статье Создание шлюза виртуальной сети](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---connect-azure-vmware-solution-to-an-on-premises-datacenter"></a>Сеть. Подключение решения VMware к локальному центру обработки данных

| Необходимая информация | Ваш ответ |
| ------------------ | ------------- |
| CIDR пиринга ExpressRoute<br><br>`/29`Блок адресов CIDR. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Ключ авторизации ExpressRoute и идентификатор ресурса<br><br>Укажите ключ авторизации и идентификатор ресурса, <br>который создается из текущего ExpressRoute <br>канал, который подключается к локальному центру обработки данных.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Направление маршрута по умолчанию<br><br>Должны ли виртуальные машины в решении Azure VMware <br>доступ к Интернету с помощью предоставленного решения VMware для Azure <br>Интернет или вернуть канал ExpressRoute к <br>локально для маршрута по умолчанию? | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Сетевые порты для взаимодействия со службой<br><br>Дополнительные сведения см. в разделе [необходимые сетевые порты](tutorial-network-checklist.md#required-network-ports). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---vmware-hcx"></a>Сети — VMware ХККС

| Необходимая информация | Ваш ответ |
| ------------------ | ------------- |
| Сетевые порты<br><br>При наличии брандмауэра убедитесь, что нужные сетевые порты <br>открываются между локальной средой и решением VMware для Azure. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| DNS<br><br>Сведения о настройке DNS см. в <br>см. раздел [Networking — инфраструктура решений VMware для Azure](#networking---azure-vmware-solution-infrastructure). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VMware ХККС Цидрс<br><br>Требуются два `/29` блока CIDR, которые используются для <br>компоненты инфраструктуры VMware ХККС в локальной среде.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |

