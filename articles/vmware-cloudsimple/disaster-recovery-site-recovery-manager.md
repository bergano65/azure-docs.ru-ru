---
title: Решения Azure VMware (AVS). Настройка частного облака AVS в качестве сайта аварийного восстановления с помощью VMware Site Recovery Manager
description: В этой статье описывается настройка частного облака AVS в качестве сайта аварийного восстановления для локальных рабочих нагрузок VMware.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bbd8a12820432ce2f131dda29af6740a2f04e18
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024727"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Настройка частного облака AVS в качестве цели аварийного восстановления с помощью VMware Site Recovery Manager

Частное облако AVS можно использовать в качестве узла аварийного восстановления для локальных рабочих нагрузок VMware.

Решение аварийного восстановления основано на репликации vSphere и VMware Site Recovery Manager (СРМ). Аналогичный подход может быть включен, чтобы включить частное облако AVS в качестве первичного сайта, защищенного локальным сайтом восстановления.

Решение AVS:

* Избавляет от необходимости настраивать центр обработки данных специально для аварийного восстановления.
* Позволяет использовать расположения Azure, в которых развернута AVS для Всемирной географической устойчивости.
* Предоставляет возможность снизить затраты на развертывание и совокупную стоимость владения для установки аварийного восстановления.

Решение AVS требует следующих действий:

* Установка, Настройка и управление репликацией vSphere и СРМ в частном облаке AVS.
* Предоставьте собственные лицензии для СРМ, если частное облако AVS является защищенным сайтом. При использовании в качестве сайта восстановления дополнительные лицензии СРМ для сайта AVS не требуются.

Это решение позволяет полностью контролировать vSphere репликацию и СРМ. Привычный интерфейс UI, API и интерфейса командной строки позволяет использовать существующие сценарии и средства.

![Развертывание диспетчера Site Recovery](media/srm-deployment.png)

Вы можете использовать любые версии вра и СРМ, совместимые с частным облаком AVS и локальными средами. В примерах этого руководств используются вра 6,5 и СРМ 6,5. Эти версии совместимы с vSphere 6,5, который поддерживается AVS.

## <a name="deploy-the-solution"></a>Развертывание решения

В следующих разделах описывается развертывание решения аварийного восстановления с помощью СРМ в частном облаке AVS.

1. [Проверка совместимости версий продуктов VMware](#verify-that-vmware-product-versions-are-compatible)
2. [Оценка размера среды аварийного восстановления](#estimate-the-size-of-your-dr-environment)
3. [Создание частного облака AVS для своей среды](#create-an-avs-private-cloud-for-your-environment)
4. [Настройка сети для частного облака AVS для решения СРМ](#set-up-avs-private-cloud-networking-for-the-srm-solution)
5. [Настройка VPN-подключения типа "сеть — сеть" между локальной сетью и частным облаком AVS и открытием необходимых портов](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports)
6. [Настройка служб инфраструктуры в частном облаке AVS](#set-up-infrastructure-services-in-your-avs-private-cloud)
7. [Установка устройства репликации vSphere в локальной среде](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Установка устройства репликации vSphere в среде частного облака AVS](#install-vsphere-replication-appliance-in-your-avs-private-cloud-environment)
9. [Установка СРМ Server в локальной среде](#install-srm-server-in-your-on-premises-environment)
10. [Установка сервера СРМ в частном облаке "AVS"](#install-srm-server-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Проверка совместимости версий продуктов VMware

На конфигурации в этом руководство распространяются следующие требования к совместимости:

* Одна и та же версия СРМ должна быть развернута в частном облаке AVS и в локальной среде.
* Одна и та же версия репликации vSphere должна быть развернута в частном облаке "AVS" и в локальной среде.
* Версии контроллера служб платформы (PSC) в частном облаке AVS и локальной среде должны быть совместимы.
* Версии vCenter в частном облаке AVS и локальной среде должны быть совместимы.
* Версии репликации СРМ и vSphere должны быть совместимы друг с другом и с версиями PSC и vCenter.

Ссылки на соответствующую документацию по VMware и сведения о совместимости можно найти в документации по [vmware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) .

Чтобы узнать версии vCenter и PSC в частном облаке AVS, откройте портал AVS. Перейдите к разделу **ресурсы**, выберите свое частное облако AVS и перейдите на вкладку **сеть управления vSphere** .

![версии vCenter & PSC в частном облаке AVS](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Оценка размера среды аварийного восстановления

1. Убедитесь, что указанная локальная конфигурация находится в пределах поддерживаемых ограничений. Для СРМ 6,5 ограничения описаны в статье базы знаний VMware об [операционных ограничениях для Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110).
2. Убедитесь в наличии достаточной пропускной способности сети в соответствии с требованиями к размеру рабочей нагрузки и RPO. Статья базы знаний VMware по [вычислению требований к пропускной способности для репликации vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) предоставляет рекомендации по ограничениям пропускной способности.
3. Используйте средство "размер AVS" для оценки ресурсов, необходимых на сайте аварийного восстановления, чтобы защитить локальную среду.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>Создание частного облака AVS для своей среды

Создайте частное облако AVS на портале AVS, следуя инструкциям и рекомендациям по выбору размера в разделе [Создание частного облака AVS](create-private-cloud.md).

### <a name="set-up-avs-private-cloud-networking-for-the-srm-solution"></a>Настройка сети для частного облака AVS для решения СРМ

Получите доступ к порталу AVS, чтобы настроить сеть частного облака AVS для решения СРМ.

Создайте виртуальную ЛС для сети решения СРМ и назначьте ей подсеть CIDR. Инструкции см. в статье [Создание виртуальных ЛС и подсетей и управление ими](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports"></a>Настройка VPN-подключения типа "сеть — сеть" между локальной сетью и частным облаком AVS и открытием необходимых портов

Настройка подключения "сеть — сеть" между локальной сетью и частным облаком AVS. Инструкции см. [в статье Настройка VPN-подключения к частному облаку AVS](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-avs-private-cloud"></a>Настройка служб инфраструктуры в частном облаке AVS

Настройте службы инфраструктуры в частном облаке AVS, чтобы упростить управление рабочими нагрузками и инструментами.

Вы можете добавить внешний поставщик удостоверений, как описано в статье [Использование Azure AD в качестве поставщика удостоверений для vCenter в частном облаке AVS](azure-ad.md) , если необходимо выполнить следующие действия.

* Выявление пользователей из локальной Active Directory (AD) в частном облаке AVS.
* Настройте AD в частном облаке AVS для всех пользователей.
* Используйте Azure AD.

Чтобы обеспечить поиск IP-адресов, управление IP-адресами и службы разрешения имен для рабочих нагрузок в частном облаке AVS, настройте DHCP-и DNS-сервер, как описано в разделе [Настройка приложений и рабочих нагрузок DNS в частном облаке AVS](dns-dhcp-setup.md).

Домен *. avs.io используется виртуальными машинами и узлами управления в частном облаке AVS. Чтобы разрешить запросы к этому домену, Настройте пересылку DNS на DNS-сервере, как описано в разделе [Создание сервера условной пересылки](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Установка устройства репликации vSphere в локальной среде

Установите устройство репликации vSphere (вра) в локальной среде, следуя документации по VMware. Установка состоит из следующих общих этапов:

1. Подготовьте локальную среду к установке вра.

2. Разверните вра в локальной среде, используя OVF в образе VR ISO из vmware.com. Для вра 6,5 [этот блог VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) содержит соответствующие сведения.

3. Зарегистрируйте локальную вра с помощью единого входа vCenter на локальном сайте. Подробные инструкции по репликации vSphere 6,5 см. в документе VMware [VMware vSphere Установка и Настройка репликации 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-avs-private-cloud-environment"></a>Установка устройства репликации vSphere в среде частного облака AVS

Перед началом убедитесь, что у вас есть следующее:

* Достижимость IP-адресов из подсетей в локальной среде в подсеть управления частного облака AVS
* Достижимость IP-адресов из подсети репликации в локальной среде vSphere в подсети решения СРМ частного облака AVS

Инструкции см. [в статье Настройка VPN-подключения к частному облаку AVS](set-up-vpn.md). Эти действия похожи на те, которые относятся к локальной установке.

При установке вра и СРМ AVS рекомендует использовать FQDN вместо IP-адресов. Чтобы узнать полное доменное имя vCenter и PSC в частном облаке AVS, откройте портал AVS. Перейдите к разделу **ресурсы**, выберите свое частное облако AVS и перейдите на вкладку **сеть управления vSphere** .

![Поиск полного доменного имени vCenter/PSC в частном облаке AVS](media/srm-resources.png)

Для AVS требуется, чтобы вы не устанавливали вра и СРМ, используя пользователя по умолчанию "клаудовнер", но вместо этого создаете нового пользователя. Это делается для того, чтобы обеспечить высокую отказоустойчивость и доступность для среды работы с частным облаком AVS. Однако пользователь клаудовнер по умолчанию в частном облаке AVS не имеет достаточных привилегий для создания нового пользователя с правами администратора.

Перед установкой вра и СРМ необходимо эскалировать привилегии vCenter для пользователя клаудовнер, а затем создать пользователя с правами администратора в домене единого входа vCenter. Дополнительные сведения о модели пользователя и разрешения частного облака по умолчанию AVS см. в [статье изучение модели разрешений частного облака AVS](learn-private-cloud-permissions.md).

Установка состоит из следующих общих этапов:

1. [Эскалировать привилегии](escalate-private-cloud-privileges.md).
2. Создайте пользователя в частном облаке AVS для репликации vSphere и установки СРМ. Описание ниже в [пользовательском интерфейсе vCenter: создание пользователя в частном облаке AVS для вра & СРМ установки](#vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation).
3. Подготовьте среду частного облака AVS к установке вра.
4. Разверните вра в частном облаке AVS с помощью OVF в образе VR ISO из vmware.com. Для вра 6,5 [этот блог VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) содержит актуальные сведения.
5. Настройте правила брандмауэра для вра. Описание ниже на [портале AVS: Настройка правил брандмауэра для вра](#avs-portal-configure-firewall-rules-for-vra).
6. Регистрация частного облака AVS вра с помощью единого входа vCenter на сайте частного облака AVS.
7. Настройка подключений репликации vSphere между двумя устройствами. Убедитесь, что нужные порты открыты через брандмауэры. Список номеров портов, которые должны быть открыты для репликации vSphere 6,5, см. в [этой статье базы знаний VMware](https://kb.vmware.com/s/article/2087769) .

Подробные инструкции по установке vSphere Replication 6,5 см. в документе VMware [VMware vSphere Установка и Настройка репликации 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation"></a>Пользовательский интерфейс vCenter: создание пользователя в частном облаке AVS для установки вра и СРМ

Войдите в vCenter с помощью учетных данных пользователя клаудовнер после повышения привилегий на портале AVS.

Создайте нового пользователя, `srm-soln-admin`, в vCenter и добавьте его в группу администраторов в vCenter.
Выйдите из vCenter в качестве пользователя клаудовнер и войдите в систему как пользователь *СРМ-солн-Admin* .

#### <a name="avs-portal-configure-firewall-rules-for-vra"></a>Портал AVS: Настройка правил брандмауэра для вра

Настройте правила брандмауэра, как описано в разделе [Настройка таблиц и правил брандмауэра](firewall.md) для открытия портов, обеспечивающих обмен данными между:

* Вра в сети решений СРМ, а также на узлах vCenter и ESXi в сети управления.
* Вра устройства на двух сайтах.

Список номеров портов, которые должны быть открыты для репликации vSphere 6,5, см. в этой [статье базы знаний VMware](https://kb.vmware.com/s/article/2087769) .

### <a name="install-srm-server-in-your-on-premises-environment"></a>Установка СРМ Server в локальной среде

Прежде чем начать, проверьте следующее.

* Устройство репликации vSphere устанавливается в средах частных облаков и в локальных облаках AVS.
* Устройства репликации vSphere на обоих сайтах соединяются друг с другом.
* Вы проверили информацию об VMware по предварительным требованиям и рекомендациям. Для СРМ 6,5 можно ознакомиться с предварительными требованиями к документам VMware и рекомендациями [по срм 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Следуйте инструкциям по работе с документацией по VMware, чтобы выполнить установку СРМ Server в топологии двух сайтов модели развертывания с одним экземпляром vCenter на контроллере служб платформы, как описано в этом [документе VMware](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Инструкции по установке для СРМ 6,5 доступны в документе VMware [установка Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-avs-private-cloud"></a>Установка сервера СРМ в частном облаке "AVS"

Прежде чем начать, проверьте следующее.

* Устройство репликации vSphere устанавливается в средах частных облаков и в локальных облаках AVS.
* Устройства репликации vSphere на обоих сайтах соединяются друг с другом.
* Вы проверили информацию об VMware по предварительным требованиям и рекомендациям. Для СРМ 6,5 можно ознакомиться с [предварительными требованиями и рекомендациями по установке сервера Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Ниже описана процедура установки частного облака AVS СРМ.

1. [Пользовательский интерфейс vCenter: install СРМ](#vcenter-ui-install-srm)
2. [Портал AVS: Настройка правил брандмауэра для СРМ](#avs-portal-configure-firewall-rules-for-srm)
3. [Пользовательский интерфейс vCenter: Configure СРМ](#vcenter-ui-configure-srm)
4. [Портал AVS: отмена эскалации привилегий](#avs-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>Пользовательский интерфейс vCenter: install СРМ

После входа в vCenter с помощью учетных данных СРМ-солн-Admin следуйте инструкциям по VMware, чтобы выполнить установку СРМ Server в топологии двух сайтов модели развертывания с одним экземпляром vCenter на контроллере служб платформы, как описано в этом [документе VMware](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Инструкции по установке для СРМ 6,5 доступны в документе VMware [установка Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="avs-portal-configure-firewall-rules-for-srm"></a>Портал AVS: Настройка правил брандмауэра для СРМ

Настройте правила брандмауэра, как описано в разделе [Настройка таблиц и правил брандмауэра](firewall.md) , чтобы разрешить обмен данными между:

Сервер СРМ и vCenter/PSC в частном облаке AVS.
Серверы СРМ на обоих сайтах

Список номеров портов, которые должны быть открыты для репликации vSphere 6,5, см. в [этой статье базы знаний VMware](https://kb.vmware.com/s/article/2087769) .

#### <a name="vcenter-ui-configure-srm"></a>Пользовательский интерфейс vCenter: Configure СРМ

После установки СРМ в частном облаке AVS выполните следующие задачи, как описано в разделах руководства по установке и настройке VMware Site Recovery Manager. Инструкции для СРМ 6,5 можно найти в документе VMware [установка Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Подключите экземпляры сервера Site Recovery Manager на защищенных узлах и сайтах восстановления.
2. Установите клиентское соединение с экземпляром сервера удаленного Site Recovery Manager.
3. Установите лицензионный ключ Site Recovery Manager.

#### <a name="avs-portal-de-escalate-privileges"></a>Портал AVS: отмена эскалации привилегий

Сведения о том, как отменять привилегии, см. в разделе [de-эскалировать Privileges](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Постоянное управление решением СРМ

Вы имеете полный контроль над vSphere репликацией и программным обеспечением СРМ в среде частного облака AVS и должны выполнять необходимое управление жизненным циклом программного обеспечения. Перед обновлением или обновлением vSphere Replication или СРМ убедитесь, что все новые версии программного обеспечения совместимы с частным облаком версий AVS и PSC.

> [!NOTE]
> В настоящее время AVS изучает параметры для предложения управляемой службы аварийного восстановления. 

## <a name="multiple-replication-configuration"></a>Конфигурация с несколькими репликациями

 [Технологии репликации на основе массивов и vSphere можно использовать одновременно с СРМ](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) . Однако они должны быть применены к отдельному набору виртуальных машин (данная виртуальная машина может быть защищена с помощью репликации на основе массива или репликации vSphere, но не обоих). Кроме того, сайт AVS можно настроить как сайт восстановления для нескольких защищенных сайтов. Сведения о конфигурациях с несколькими сайтами см. в разделе [СРМ Multi-Site Options](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) .

## <a name="references"></a>Ссылки

* [Документация по VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Операционные ограничения для Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110)
* [Вычисление требований к пропускной способности для репликации vSphere](https://kb.vmware.com/s/article/2037268)
* [Варианты OVF при развертывании vSphere Replication 6,5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [Установка и настройка VMware vSphere репликации 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Предварительные требования и рекомендации для СРМ 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site Recovery Manager в топологии с двумя сайтами с одним экземпляром vCenter Server на контроллер служб платформы](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Руководства по установке и настройке VMware Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [Блог VMware по СРМ с репликацией на основе массивов и репликацией vSphere](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [Блог по VMware о вариантах СРМ нескольких сайтов](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Номера портов, которые должны быть открыты для vSphere репликации 5.8. x, 6. x и 8](https://kb.vmware.com/s/article/2147112)
