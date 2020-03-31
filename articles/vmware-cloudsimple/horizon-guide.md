---
title: Решение Azure VMware от CloudSimple - Используйте сайт Private Cloud для размещения виртуальной настольной инфраструктуры с использованием VMware Horizon
description: Описывает, как можно использовать сайт CloudSimple Private Cloud для размещения виртуальной настольной инфраструктуры с помощью VMware Horizon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025254"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Используйте сайт CloudSimple Private Cloud для размещения виртуальной настольной инфраструктуры с помощью VMware Horizon

Вы можете использовать свой сайт CloudSimple Private Cloud для размещения виртуальной инфраструктуры настольных компьютеров (VDI) с помощью VMware Horizon 7.x. Следующая цифра показывает архитектуру логического решения для VDI.

![Развертывание горизонта](media/horizon-deployment.png)

С помощью этого решения вы имеете полный контроль над Horizon View Manager и объемом приложений. Знакомые интерфейсы UI, API и CLI позволяют использовать существующие скрипты и инструменты.

Решение CloudSimple требует от вас следующего:

* Установите, направляйте и управляйте VMware Horizon 7.x в личном облаке.
* Предоставьте свои собственные лицензии Horizon.

## <a name="deploy-the-solution"></a>Развертывание решения

В следующих разделах описывается, как развернуть решение VDI с помощью Horizon в личном облаке.

1. [Проверить, что версии продуктов VMware совместимы](#verify-that-vmware-product-versions-are-compatible)
2. [Оцените размер среды рабочего стола](#estimate-the-size-of-your-desktop-environment)
3. [Создание частного облака для вашей среды](#create-a-private-cloud-for-your-environment)
4. [Установка VMware Horizon в приватном облаке](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Проверить, что версии продуктов VMware совместимы

* Убедитесь, что ваши текущие и планируемые версии Horizon, Объемы приложений, Unified Access Gateway и менеджер пользовательской среды совместимы друг с другом, а также с vCenter и PSC в частном облаке. Для получения информации [VMware Compatibility Matrix for Horizon 7.5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=)о совместимости см.
* Чтобы узнать текущие версии vCenter и PSC в личном облаке, перейдите на **портал Ресурсов** в [CloudSimple,](access-cloudsimple-portal.md)выберите private Cloud и нажмите на вкладку **vSphere Management Network.**

![vCenter и PSC версии](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Оцените размер среды рабочего стола

* Убедитесь, что идентифицированная конфигурация находится в пределах работы VMware.
* Оцените ресурсы, необходимые для всех настольных компьютеров и компонентов управления Horizon.

### <a name="create-a-private-cloud-for-your-environment"></a>Создание частного облака для вашей среды

1. Создайте частное облако с портала CloudSimple, следуя инструкциям в [настройке среды private Cloud.](quickstart-create-private-cloud.md)  CloudSimple создает пользователя vCenter по умолчанию под названием «облачный владелец» в каждом недавно созданном частном облаке. Подробнее о модели конфиденциального облака по [Learn the Private Cloud permissions model](learn-private-cloud-permissions.md)умолчанию и модели разрешений см.
2. Создайте VLAN в частном облаке для плоскости управления Horizon и назначьте ему подсеть CIDR. Для получения инструкций смотрите [Создание и управление VLANs/Subnets.](create-vlan-subnet.md) Это сеть, в которой будут установлены все компоненты решения (Unified Access Gateway, Сервер подключения, сервер объемов приложений и серверы менеджера по вопросам пользовательской среды).
3. Решите, хотите ли вы использовать внешнего поставщика идентификационных данных с помощью частного облачного vCenter. Если да, выберите один из этих вариантов:
    * Используйте свой предпосылок Active Directory в качестве внешнего поставщика идентификационных данных. Для получения инструкций [см.](set-vcenter-identity.md)
    * Настройка сервера Active Directory в плоскости управления Private Cloud in Horizon VLAN для использования в качестве внешнего поставщика идентификационных данных. Для получения инструкций [см.](set-vcenter-identity.md)
    * Настройка сервера DHCP и DNS в плоскости управления Horizon VLAN в частном облаке. Для получения инструкций смотрите [настройку DNS и DHCP-приложений и рабочих нагрузок в облачном частном облаке.](dns-dhcp-setup.md)
4. Налаживание DNS-пересылки на сервере DNS, установленном в частном облаке. Для инструкций [см.](on-premises-dns-setup.md#create-a-conditional-forwarder)

### <a name="install-vmware-horizon-in-your-private-cloud"></a>Установка VMware Horizon в приватном облаке

Следующая диаграмма развертывания изображает решение Horizon, развернутое в приватном облаке. Единый шлюз доступа, AD/DC, View и App Volume Server установлены в созданном пользователем VLAN 234. Unified Access Gateway имеет назначенный публичный IP-адрес, доступный из Интернета. Для обеспечения дополнительной изоляции и безопасности в VLAN 235 развернуты настольные вшивки Horizon.

![Развертывание Horizon в приватном облаке](media/horizon-private-cloud.png)

В следующих разделах изложены инструкции по настройке развертывания, аналогичного тому, которое изображено на рисунке. Прежде чем начать, убедитесь, что у вас есть следующее:

* Частное облако, созданное с помощью портала CloudSimple с достаточной емкостью для запуска настольных пулов.
* Достаточная пропускная способность между вашей предварительной средой и средой Private Cloud для поддержки сетевого трафика для настольных компьютеров.
* VPN-туннель от сайта к сайту, созданный между вашим центром обработки данных и частным облаком.
* Доступность IP от подсетей конечных пользователей в предприимчивой среде до подсетей CloudSimple Private Cloud.
* AD/DHCP/DNS установлен для вашего частного облака.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple портал: Создание специального VLAN /субнета для настольных бассейнов

Создайте VLAN для настольных пулов Horizon и назначайте ему подсеть CIDR. Для получения инструкций смотрите [Создание и управление VLANs/Subnets.](create-vlan-subnet.md) Это сеть, где будут работать все настольные виртуальные машины.

Соблюдайте стандартные рекомендации по безопасности, чтобы обеспечить развертывание Horizon:

* Разрешить только настольный трафик RDP / SSH трафик на рабочем столе VMs.
* Разрешить только управление трафиком между самолетом управления Horizon VLAN и настольным пулом VLAN.
* Разрешить только управление трафиком из находинной сети.

Вы можете применять эти рекомендации, настраивая [правила брандмауэра](firewall.md) с портала CloudSimple.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>Портал CloudSimple: Налажёте правила брандмауэра для безопасности плоскости управления Horizon

Навлажай следующие правила на портале CloudSimple. Для инструкций смотрите [настройку таблиц и правил брандмауэра.](firewall.md)

1. Назначайте правила брандмауэра в брандмауэре CloudSimple N-S, чтобы обеспечить связь между надводными подсетями и управлением Horizon VLAN, чтобы можно было разрешить только сетевые порты, перечисленные в [списке портов](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) горизонта документов VMware.

2. Создание правил брандмауэра E-W между управлением Horizon VLAN и настольным пулом VLAN в приватном облаке.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>CloudSimple портал: Создание общедоступного IP-адреса для единого шлюза доступа

Создайте общедоступный IP-адрес для единого приложения Access Gateway, чтобы включить соединения настольных клиентов из Интернета. Для получения инструкций [см.](public-ips.md)

По завершении настройки назначается общедоступный IP-адрес, который указан на странице ip-адресов общественности.

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple портал: Эскалация привилегий

Пользователь "облачного владельца" по умолчанию не имеет достаточных привилегий в Private Cloud vCenter для установки Horizon, поэтому привилегии vCenter пользователя должны быть повышены. Для получения дополнительной [информации см.](escalate-private-cloud-privileges.md)

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>интерфейс vCenter: Создайте пользователя в частном облаке для установки Horizon

1. Войти в vCenter, используя учетные данные пользователей "облачного владельца".
2. Создайте нового пользователя, 'горизонт-Soln-admin', в vCenter и добавьте пользователя в группу администраторов в vCenter.
3. Выйти из vCenter в качестве пользователя "облачного владельца" и войти в качестве пользователя "горизонт-soln-admin".

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI: Установка VMware Horizon

Как упоминалось в предыдущем разделе логической архитектуры, решение Horizon имеет следующие компоненты:

* Вид на горизонт VMware
* VMware Единый шлюз доступа
* Менеджер по объему приложений VMware
* Менеджер по среде пользователей VMware

Установите компоненты следующим образом:

1. Установите и направите единый шлюз доступа, следуя инструкциям, приведенным в документе VMware [Развертывание и настройка единого шлюза доступа VMware.](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)

2. Установите Horizon View в приватном облаке, следуя инструкциям в [Руководстве по установке view.](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)

3. Установите менеджер громкости приложений, следуя инструкциям в [установке и настройке vMware App Volumes.](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)

4. Установите и нанастройки менеджера по среде пользователя, следуя инструкциям [по установке и настройке менеджера по среде пользователя VMware.](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Подать запрос на поддержку для загрузки vMware Horizon расфасованных объемов приложений

В процессе установки менеджер по объему приложений использует предварительно упакованные тома для предоставления стеков приложений и объемов. Эти тома служат шаблонами для стеков приложений и объемов.

Для загрузки томов в хранилище данных Private Cloud требуется корневой пароль ESXi. Для получения помощи отправьте [запрос на поддержку.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Прикрепите комплект установки AppVolumes, чтобы сотрудники службы поддержки CloudSimple могли загружать шаблоны в среду Private Cloud.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>Портал CloudSimple: Деэскалация привилегий

Теперь можно [деэскалировать привилегии](escalate-private-cloud-privileges.md#de-escalate-privileges) пользователя "облачного владельца".

## <a name="ongoing-management-of-your-horizon-solution"></a>Постоянное управление решением Horizon

Вы имеете полный контроль над программным обеспечением Horizon и App Volume Manager в среде Private Cloud и должны выполнять необходимое управление жизненным циклом программного обеспечения. Убедитесь, что любые новые версии программного обеспечения совместимы с Private Cloud vCenter и PSC перед обновлением или обновлением Horizon или Объем омрачаем приложений.
