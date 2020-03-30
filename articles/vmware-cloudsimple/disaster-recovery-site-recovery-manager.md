---
title: Решение Azure VMware от CloudSimple - Настройка частного облака в качестве места аварийного восстановления с помощью менеджера восстановления сайта VMware
description: Описывает, как настроить облачное частное облако в качестве места аварийного восстановления для рабочих нагрузок VMware
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa8b2da683d68a337df38e13726f22c5af43540a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565933"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Настройка частного облака в качестве цели аварийного восстановления с помощью менеджера по восстановлению сайта VMware

Вы можете использовать облачное частное облако в качестве места аварийного восстановления (DR) для работы на местах VMware.

Решение DR основано на vSphere Replication и менеджере восстановления сайта VMware (SRM). Аналогичный подход можно использовать для включения вашего частного облака в качестве основного сайта, защищенного вашим сайтом восстановления.

Решение CloudSimple:

* Устраняет необходимость создания центра обработки данных специально для DR.
* Позволяет использовать места Azure, где CloudSimple развернут для географической устойчивости во всем мире.
* Предоставляет возможность сократить затраты на развертывание и общую стоимость владения для создания DR.

Решение CloudSimple требует от вас следующего:

* Установка, настройка и управление репликацией vSphere и SRM в вашем частном облаке.
* Предоставьте свои собственные лицензии для SRM, когда частное облако является защищенным сайтом. Вам не нужны дополнительные лицензии SRM для сайта CloudSimple, когда он используется в качестве сайта восстановления.

С помощью этого решения, у вас есть полный контроль над репликации vSphere и SRM. Знакомые интерфейсы UI, API и CLI позволяют использовать существующие скрипты и инструменты.

![Развертывание менеджера восстановления сайта](media/srm-deployment.png)

Вы можете использовать любые версии vRA и SRM, совместимые с вашим частным облаком и на территории. В приведенных примерах используются vRA 6.5 и SRM 6.5. Эти версии совместимы с vSphere 6.5, который поддерживается CloudSimple.

## <a name="deploy-the-solution"></a>Развертывание решения

В следующих разделах описывается, как развернуть решение DR с помощью SRM в личном облаке.

1. [Проверить, что версии продуктов VMware совместимы](#verify-that-vmware-product-versions-are-compatible)
2. [Оцените размер среды DR](#estimate-the-size-of-your-dr-environment)
3. [Создание частного облака для вашей среды](#create-a-private-cloud-for-your-environment)
4. [Настройка сети Private Cloud для решения SRM](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Настройка VPN-соединения сайта к сайту между вашей предварительной сетью и частным облаком и открытыми необходимыми портами](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Настройка инфраструктурных служб в приватном облаке](#set-up-infrastructure-services-in-your-private-cloud)
7. [Установка прибора vSphere Replication в вашей предварительной среде](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Установка прибора репликации vSphere в среде private Cloud](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Установка SRM-сервера в среде](#install-srm-server-in-your-on-premises-environment)
10. [Установка SRM-сервера в приватном облаке](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Проверить, что версии продуктов VMware совместимы

Конфигурации в этом руководстве подпадают под следующие требования к совместимости:

* Та же версия SRM должна быть развернута в вашем частном облаке и в вашей предварительной среде.
* Та же версия репликации vSphere должна быть развернута в вашем частном облаке и в вашей предварительной среде.
* Версии контроллера служб платформы (PSC) в вашем частном облаке и в предварительной среде должны быть совместимы.
* Версии vCenter в вашем частном облаке и в предварительной среде должны быть совместимы.
* Версии репликации SRM и vSphere должны быть совместимы друг с другом и с версиями PSC и vCenter.

Для получения ссылок на соответствующую документацию VMware и информацию о совместимости перейдите в документацию [менеджера по восстановлению сайта VMware.](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)

Чтобы узнать версии vCenter и PSC в личном облаке, откройте портал CloudSimple. Перейдите к **ресурсам,** выберите частное облако и нажмите на вкладку **vSphere Management Network.**

![vCenter & версии PSC в приватном облаке](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Оцените размер среды DR

1. Убедитесь, что идентифицированная конфигурация в закрытом помещении находится в пределах поддержки. Для SRM 6.5 ограничения описаны в статье базы знаний VMware о [эксплуатационных ограничениях для менеджера восстановления сайта 6.5](https://kb.vmware.com/s/article/2147110).
2. Убедитесь, что у вас достаточно пропускной способности сети для удовлетворения размера рабочей нагрузки и требований RPO. Статья базы знаний VMware по [расчету требований к пропускной способности для vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) содержит рекомендации по ограничениям пропускной способности.
3. Используйте инструмент CloudSimple sizer для оценки ресурсов, необходимых на вашем сайте DR, чтобы защитить вашу среду.

### <a name="create-a-private-cloud-for-your-environment"></a>Создание частного облака для вашей среды

Создайте частное облако с портала CloudSimple, следуя инструкциям и рекомендациям по размеру в [«Создании частного облака».](create-private-cloud.md)

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Настройка сети Private Cloud для решения SRM

Доступ к порталу CloudSimple для создания сети Private Cloud для решения SRM.

Создайте VLAN для сети решений SRM и назначайте ей подсеть CIDR. Для получения инструкций смотрите [Создание и управление VLANs/Subnets.](create-vlan-subnet.md)

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Настройка VPN-соединения сайта к сайту между вашей предварительной сетью и частным облаком и открытыми необходимыми портами

Настройка подключения от сайта к сайту между вашей предварительной сетью и вашим частным облаком. Для получения инструкций смотрите [нанастройку VPN-соединения с вашим облачным частным облаком.](set-up-vpn.md)

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Настройка инфраструктурных служб в приватном облаке

Налаживайте инфраструктурные службы в приватном облаке, чтобы упростить управление рабочими нагрузками и инструментами.

Вы можете добавить внешнего поставщика идентификационных данных, как описано в [Use Azure AD, в качестве поставщика идентификационных данных для vCenter в облачном частном облаке,](azure-ad.md) если вы хотите сделать любое из следующих:

* Определите пользователей из вашего предпосылок Active Directory (AD) в личном облаке.
* Навладив AD в приватном облаке для всех пользователей.
* Используйте Azure AD.

Чтобы обеспечить поиск IP-адресов, управление IP-адресами и службы разрешения имен для ваших рабочих нагрузок в приватном облаке, навязайте сервер DHCP и DNS, как описано в [приложениях Setup DNS и DHCP, а также рабочие нагрузки в облачном частном облаке.](dns-dhcp-setup.md)

Домен cloudsimple.io используется в управлении VMs и хостами в вашем частном облаке. Для решения запросов на этот домен назначайте переадресировку DNS на сервере DNS, как описано в [Create a Conditional Forwarder.](on-premises-dns-setup.md#create-a-conditional-forwarder)

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Установка vSphere Репликационный прибор в вашей предварительной среде

Установите vSphere Replication Appliance (vRA) в вашей предварительной среде, следуя документации VMware. Установка состоит из следующих шагов высокого уровня:

1. Подготовьте среду для установки vRA.

2. Развертывание vrRA в вашей предварительной среде с помощью OVF в VR ISO из vmware.com. Для vRA 6.5, [этот блог VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) имеет соответствующую информацию.

3. Зарегистрируйте свой наместах vRA с vCenter Single Sign-On на месте. Для получения подробных инструкций для репликации vSphere 6.5 см. [VMware vSphere Replication 6.5 Installation and Configuration](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>Установка прибора репликации vSphere в среде private Cloud

Прежде чем начать, убедитесь, что у вас есть следующее:

* Доступность IP от подсетей в вашей среде до подсети управления вашим частным облаком
* Доступность IP от подсети репликации в вашей предварительной среде vSphere к подсети решений SRM вашего частного облака

Для получения инструкций смотрите [нанастройку VPN-соединения с вашим облачным частным облаком.](set-up-vpn.md) Шаги аналогичны тем, которые предулок установки.

CloudSimple рекомендует использовать ФЗН вместо IP-адресов во время установки vRA и SRM. Чтобы узнать, как в частном облаке и psC, можно узнать о F-DN vCenter и PSC, откройте портал CloudSimple. Перейдите к **ресурсам,** выберите частное облако и нажмите на вкладку **vSphere Management Network.**

![Поиск ФЦДН vCenter/PSC в частном облаке](media/srm-resources.png)

CloudSimple требует, чтобы вы не устанавливали vRA и SRM с помощью пользователя "облачного владельца" по умолчанию, а вместо этого создали нового пользователя. Это делается для обеспечения высокого времени простоя и доступности для вашей среды Private Cloud vCenter. Однако пользователь облачного владельца по умолчанию в Private Cloud vCenter не имеет достаточных привилегий для создания нового пользователя с административными привилегиями.

Перед установкой vRA и SRM необходимо усилить привилегии vCenter пользователя облачного владельца, а затем создать пользователя с административными привилегиями в домене vCenter SSO. Подробнее о модели пользовательского частного облака по умолчанию и модели разрешений смотрите [в модели разрешения Private Cloud.](learn-private-cloud-permissions.md)

Установка состоит из следующих шагов высокого уровня:

1. [Эскалация привилегий](escalate-private-cloud-privileges.md).
2. Создайте пользователя в личном облаке для репликации vSphere и установки SRM. Объясняется ниже в [vCenter UI: Создайте пользователя в частном облаке для установки vRA & SRM.](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation)
3. Подготовьте среду Private Cloud для установки vRA.
4. Развертывание vRA в личном облаке с помощью OVF в VR ISO с vmware.com. Для vRA 6.5, [этот блог VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) имеет соответствующую информацию.
5. Налажить правила брандмауэра для vRA. Объясняется ниже в [CloudSimple портал: Настроить правила брандмауэра для vRA](#cloudsimple-portal-configure-firewall-rules-for-vra).
6. Зарегистрируйте private Cloud vRA с vCenter Single Sign-On на сайте Private Cloud.
7. Налаживать vSphere репликации соединения между двумя приборами. Убедитесь, что необходимые порты открыты через брандмауэры. Смотрите [эту статью базы знаний VMware](https://kb.vmware.com/s/article/2087769) для списка номеров портов, которые должны быть открыты для репликации vSphere 6.5.

Для получения подробных инструкций по установке для репликации [VMware vSphere Replication 6.5 Installation and Configuration](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)vSphere 6.5 см.

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>интерфейс vCenter: Создайте пользователя в частном облаке для установки vRA и SRM

Войдя в vCenter, используя учетные данные пользователей Cloudowner после эскалации привилегий с портала CloudSimple.

Создайте нового `srm-soln-admin`пользователя в vCenter и добавьте его в группу администраторов в vCenter.
Выйти из vCenter в качестве пользователя облачного владельца и войти в качестве пользователя *srm-soln-admin.*

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple портал: Настроить правила брандмауэра для vRA

Нанимите правила брандмауэра, описанные в [таблицах и правилах настройки брандмауэра,](firewall.md) чтобы открыть порты для связи между:

* vRA в сети решений SRM и vCenter и ESXi хостов в сети управления.
* vRA приборов на двух сайтах.

Смотрите эту [статью базы знаний VMware](https://kb.vmware.com/s/article/2087769) для списка номеров портов, которые должны быть открыты для репликации vSphere 6.5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Установка SRM-сервера в среде

Прежде чем начать, проверьте следующее:

* vSphere Replication Appliance устанавливается в ваших средах на территории и в частном облаке.
* Приборы репликации vSphere на обоих участках соединены друг с другом.
* Вы изучили информацию VMware о предпосылках и рекомендациях. Для SRM 6.5 вы можете обратиться к документу VMware [Prerequisites и рекомендациям для SRM 6.5.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)

Следуйте документации VMware для выполнения установки сервера SRM в модели развертывания 'Двухместная топология с одним vCenter Instance на контроллер служб платформы', как описано в этом [документе VMWare.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html) Инструкции по установке для SRM 6.5 доступны в документе VMware [Установка менеджера восстановления сайта.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)

### <a name="install-srm-server-in-your-private-cloud"></a>Установка SRM-сервера в приватном облаке

Прежде чем начать, проверьте следующее:

* vSphere Replication Appliance устанавливается в ваших средах на территории и в частном облаке.
* Приборы репликации vSphere на обоих участках соединены друг с другом.
* Вы изучили информацию VMware о предпосылках и рекомендациях. Для SRM 6.5 вы можете обратиться к [предпосылкам и рекомендациям для менеджера по восстановлению сайта 6.5 Server Installation.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)

Следующие шаги описывают установку Private Cloud SRM.

1. [vCenter UI: Установка SRM](#vcenter-ui-install-srm)
2. [CloudSimple портал: Настроить правила брандмауэра для SRM](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter UI: Настройка SRM](#vcenter-ui-configure-srm)
4. [Портал CloudSimple: деэскалация привилегий](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI: Установка SRM

После входа в vCenter с использованием srm-soln-администратора учетных данных, следуйте документации VMware для выполнения SRM серверной установки в модели развертывания 'Двухместная топология с одним vCenter Instance на контроллер услуг платформы ', как описано в этом [документе VMWare](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Инструкции по установке для SRM 6.5 доступны в документе VMware [Установка менеджера восстановления сайта.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple портал: Настроить правила брандмауэра для SRM

Назначай правила брандмауэра, описанные в [таблицах и правилах настройки брандмауэра,](firewall.md) чтобы обеспечить связь между:

Сервер SRM и vCenter / PSC в частном облаке.
Серверы SRM на обоих сайтах

Смотрите [эту статью базы знаний VMware](https://kb.vmware.com/s/article/2087769) для списка номеров портов, которые должны быть открыты для репликации vSphere 6.5.

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI: Настройка SRM

После установки SRM в частном облаке выполните следующие задачи, описанные в разделах руководства по установке и настройке менеджера по восстановлению сайта VMware. Для SRM 6.5 инструкции доступны в документе VMware [Установка менеджера восстановления сайта.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)

1. Подключите серверы диспетчера восстановления сайта на защищенных и восстановительных сайтах.
2. Установить подключение клиента к удаленному серверу управления удаленного управления сайтом.
3. Установите ключ лицензии менеджера по восстановлению сайта.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>Портал CloudSimple: Деэскалация привилегий

Для деэскалации привилегий [см.](escalate-private-cloud-privileges.md#de-escalate-privileges)

## <a name="ongoing-management-of-your-srm-solution"></a>Постоянное управление вашим решением SRM

Вы имеете полный контроль над vSphere Replication и программным обеспечением SRM в вашей среде Private Cloud и должны выполнять необходимое управление жизненным циклом программного обеспечения. Убедитесь, что любая новая версия программного обеспечения совместима с Private Cloud vCenter и PSC перед обновлением или обновлением репликации vSphere или SRM.

> [!NOTE]
> В настоящее время CloudSimple изучает варианты предоставления управляемого услуги DR. 

## <a name="multiple-replication-configuration"></a>Конфигурация нескольких репликаций

 [Технологии репликации на основе массивов и репликации vSphere могут использоваться вместе с SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) одновременно. Тем не менее, они должны быть применены к отдельному набору VMs (данный VM может быть защищен либо репликацией на основе массива, либо репликацией vSphere, но не обоими). Кроме того, сайт CloudSimple может быть настроен как сайт восстановления для нескольких защищенных сайтов. Ознакомьтесь с несколькими конфигурациями [SRM Multi-Site Options.](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/)

## <a name="references"></a>Ссылки

* [Документация менеджера по восстановлению сайта VMware](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Оперативные лимиты для менеджера по восстановлению сайта 6.5](https://kb.vmware.com/s/article/2147110)
* [Расчет требований к пропускной способности для репликации vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [ВЫБОР OVF при развертывании репликации vSphere 6.5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere Репликация 6.5 Установка и конфигурация](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Предпосылки и лучшие практики для SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Менеджер восстановления сайта в двух-сайт топологии с одним vCenter Server Instance на контроллер служб платформы](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [VMware менеджер по восстановлению сайта 6.5 Установка и конфигурация Руководство](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [VMware блог о SRM с массивом на основе репликации против vSphere репликации](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [VMware блог о SRM Многосайт варианты](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Номера портов, которые должны быть открыты для репликации vSphere 5.8.x, 6.x и 8](https://kb.vmware.com/s/article/2147112)
