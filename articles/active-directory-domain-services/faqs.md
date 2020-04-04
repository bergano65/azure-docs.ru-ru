---
title: Часто задаваемые вопросы об службах домена Azure AD Документы Майкрософт
description: Прочитайте и поймите некоторые из часто задаваемых вопросов о конфигурации, администрировании и доступности для служб доменов Active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: 86b68b794928900717bea25623e7eb833c23e86c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655350"
---
# <a name="frequently-asked-questions-faqs"></a>Часто задаваемые вопросы (FAQ)

Эта страница часто отвечает на часто задаваемые вопросы об службах домена Active Directory.

## <a name="configuration"></a>Параметр Configuration

* [Можно ли создать несколько управляемых доменов для одного каталога Azure AD?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Могу ли я включить службы домена Azure AD в классическую виртуальную сеть?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Можно ли включить доменные службы Azure AD в виртуальной сети Azure Resource Manager?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Могу ли я перенести существующий управляемый домен из классической виртуальной сети в виртуальную сеть Resource Manager?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Можно ли включить доменные службы Azure AD в подписку Azure CSP (поставщик облачных решений)?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Могу ли я включить службы домена Azure AD в каталоге Federated Azure AD? Я не синхронизирую хэши паролей с Azure AD. Могу ли я включить службы домена Azure AD для этого каталога?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Можно ли сделать доменные службы AD Azure доступными в нескольких виртуальных сетях в рамках подписки?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Можно ли включить доменные службы Azure AD с помощью PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Могу ли я включить службы домена Azure AD с помощью шаблона менеджера ресурсов?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Могу ли я добавить контроллеры домена в управляемый домен доменных служб Azure AD?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Могут ли гостевые пользователи, приглашенные в каталог, использовать доменные службы Azure AD?](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [Могу ли я переместить существующий домен Домена Azure AD Domain Services в другую группу ресурсов, регион или виртуальную сеть?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Включают ли службы домена Azure AD варианты высокой доступности?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Можно ли создать несколько управляемых доменов для одного каталога Azure AD?
Нет. Для одного каталога Azure AD можно создать только один управляемый домен, поддерживаемый доменными службами Azure AD.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Могу ли я включить службы домена Azure AD в классическую виртуальную сеть?
Классические виртуальные сети не поддерживаются для новых развертываний. Существующие управляемые домены, развернутые в виртуальных сетях Classic, продолжают поддерживаться до тех пор, пока они не будут выведены из эксплуатации 1 марта 2023 года. Для существующих развертываний можно [перенести службы домена Azure AD из модели виртуальной сети Classic в ресурсный менеджер.](migrate-from-classic-vnet.md)

Для получения дополнительной информации, [см.](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Можно ли включить доменные службы Azure AD в виртуальной сети Azure Resource Manager?
Да. Доменные службы Azure AD можно включить в виртуальной сети Azure Resource Manager. Виртуальные сети Classic Azure больше не доступны при создании управляемого домена.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Могу ли я перенести существующий управляемый домен из виртуальной сети Classic в виртуальную сеть Resource Manager?
Да. Для получения дополнительной информации [см.](migrate-from-classic-vnet.md)

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Можно ли включить доменные службы Azure AD в подписку Azure CSP (поставщик облачных решений)?
Да. Для получения дополнительной информации узнайте, [как включить службы домена Azure AD в подписках Azure CSP.](csp.md)

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Можно ли включить доменные службы Azure AD в федеративном каталоге Azure AD? Хэши паролей не синхронизируются с Azure AD. Можно включить доменные службы Azure AD для этого каталога?
Нет. Для проверки подлинности пользователей с помощью NTLM или Kerberos службам домена Azure AD необходим доступ к хэшу паролей учетных записей пользователей. В каталоге federated хэши паролей не хранятся в каталоге Azure AD. Поэтому службы домена Azure AD не работают с такими каталогами Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Можно ли сделать доменные службы AD Azure доступными в нескольких виртуальных сетях в рамках подписки?
Сама служба не поддерживает этот сценарий напрямую. В определенный момент времени управляемый домен доступен только в одной виртуальной сети. Тем не менее, можно настроить подключение между несколькими виртуальными сетями, чтобы предоставить службы домена Azure AD другим виртуальным сетям. Для получения дополнительной информации, см. [how to connect virtual networks in Azure using VPN gateways](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) [virtual network peering](../virtual-network/virtual-network-peering-overview.md)

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Можно ли включить доменные службы Azure AD с помощью PowerShell?
Да. Для получения дополнительной информации узнайте, [как включить службы домена Azure AD с помощью PowerShell.](powershell-create-instance.md)

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Можно ли включить доменные службы Azure AD с помощью шаблона Resource Manager?
Yes, you can create an Azure AD Domain Services managed domain using a Resource Manager template. Перед развертыванием шаблона необходимо создать основную службу и группу Azure AD для администрирования с помощью портала Azure или Azure PowerShell. Для получения дополнительной информации [см. Создать управляемый домен Azure AD DS с помощью шаблона Управления ресурсами Azure.](template-create-instance.md) При создании домена Azure AD Domain Services, управляемого на портале Azure, также существует возможность экспорта шаблона для использования с дополнительными развертываниями.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Могу ли я добавить контроллеры домена в управляемый домен доменных служб Azure AD?
Нет. Домен, предоставленный доменными службами Azure AD — это управляемый домен. Вам не нужно предоставлять, настраивать или иным образом управлять контроллерами домена для этого домена. Эти действия по управлению предоставляются корпорацией Майкрософт в качестве услуги. Таким образом, вы не можете добавить дополнительные контроллеры домена (читать-написать или читать только) для управляемого домена.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Могут ли гостевые пользователи, приглашенные в каталог, использовать доменные службы Azure AD?
Нет. Гостевые пользователи, приглашенные в каталог Azure AD с помощью процесса [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md), синхронизируются в управляемом домене доменных служб Azure AD. Однако пароли для этих пользователей не хранятся в каталоге Azure AD. Таким образом, службы домена Azure AD не могут синхронизировать хэши NTLM и Kerberos для этих пользователей в управляемый домен. Такие пользователи не могут войти или присоединить компьютеры к управляемому домену.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Могу ли я переместить существующий домен Домена Azure AD Domain Services в другую группу ресурсов, регион или виртуальную сеть?
Нет. После создания домена Azure AD Domain Domain Services нельзя перемещать экземпляр в другую группу ресурсов, виртуальную сеть, подписку и т.д. Позаботьтесь о выборе наиболее подходящей подписки, группы ресурсов, региона и виртуальной сети при развертывании экземпляра Azure AD DS.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Включают ли службы домена Azure AD варианты высокой доступности?

Да. Каждый домен домена Azure AD Domain Services включает в себя два контроллера домена. Вы не управляете или подключаетесь к этим контроллерам домена, они являются частью управляемой службы. При развертывании служб доменов Azure AD в регионе, поддерживающем зоны доступности, контроллеры домена распределяются по зонам. В регионах, не поддерживающих зоны доступности, контроллеры доменов распределены по наборам доступности. У вас нет вариантов конфигурации или управления этим распределением. Для получения дополнительной [информации](../virtual-machines/windows/availability.md)см.

## <a name="administration-and-operations"></a>Администрация и операции

* [Можно ли подключиться к контроллеру управляемого домена с помощью удаленного рабочего стола?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Я включил службы домена Azure AD. Какую учетную запись пользователя я использую для соединения компьютеров домена с этим доменом?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Есть ли у меня привилегии администратора домена для управляемого домена, предоставляемого доменными службами AD Azure?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Можно ли изменить членство в группах с помощью LDAP или других инструментов администрирования AD для управляемых доменов?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Через какое время изменения в каталоге Azure AD будут отображаться в управляемом домене?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Могу ли я расширить схему управляемого домена, предоставляемого доменными службами Azure AD?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Можно ли изменять или добавлять записи DNS в управляемом домене?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Что из себя представляет политика времени существования пароля в управляемом домене?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Предоставляют ли доменные службы Azure AD защиту учетной записи AD с помощью блокировки?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Можно ли подключиться к контроллеру управляемого домена с помощью удаленного рабочего стола?
Нет. У вас нет разрешений на подключение к контроллерам доменов для управляемого домена с помощью удаленного рабочего стола. Члены *группы администраторов AAD DC* могут управлять управляемым доменом с помощью инструментов администрирования AD, таких как Центр администрирования активного каталога (ADAC) или AD PowerShell. Эти инструменты устанавливаются с помощью функции *удаленного администрирования сервера* на сервере Windows, объединенном к управляемому домену. Для получения дополнительной информации [см. Создать управление VM для настройки и администрирования домена Azure AD Domain Services.](tutorial-create-management-vm.md)

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Я включил службы домена Azure AD. Какой учетной записью пользователя нужно воспользоваться для подключения компьютеров к этому домену?
Присоединиться к VM может любая учетная запись пользователя, войдя в управляемый домен Azure AD DS. Членам *группы администраторов AAD DC* предоставляется удаленный доступ к настольным компьютерам, которые были соединены с управляемым доменом.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Есть ли у меня привилегии администратора домена для управляемого домена, предоставляемого доменными службами AD Azure?
Нет. Вам не предоставляются административные привилегии в управляемом домене. Привилегии *администратора домена* и *администратора предприятия* недоступны для использования в домене. Членам администратора домена или группам администраторов предприятий в вашем предварительном Active Directory также не предоставляются привилегии домена/корпоративного администратора в управляемом домене.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Можно ли изменить членство в группах с помощью LDAP или других инструментов администрирования AD для управляемых доменов?
Пользователи и группы, синхронизированные с Active Directory Azure в службы домена Azure AD, не могут быть изменены, поскольку их источником происхождения является Active Directory Azure. Любой пользователь или группа, исходящих из управляемого домена, могут быть изменены.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Через какое время изменения в каталоге Azure AD будут отображаться в управляемом домене?
Изменения, внесенные в каталог Azure AD с использованием uI Azure AD или PowerShell, автоматически синхронизируются с управляемым доменом. Процесс синхронизации происходит в фоновом режиме. Для завершения всех изменений объекта нет определенного периода времени для завершения всех изменений объекта.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Могу ли я расширить схему управляемого домена, предоставляемого доменными службами Azure AD?
Нет. Схемой управляемого домена управляет корпорация Майкрософт. Расширения Schema не поддерживаются службами домена Azure AD.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Можно ли изменять или добавлять записи DNS в управляемом домене?
Да. Членам *группы администраторов AAD DC* предоставляются привилегии *Администратора DNS* для изменения записей DNS в управляемом домене. Эти пользователи могут использовать консоль DNS Manager на машине под управлением Windows Server, присоединенной к управляемому домену для управления DNS. Чтобы использовать консоль DNS Manager, установите *DNS Server Tools,* которая является частью дополнительной функции *удаленного администрирования сервера* на сервере. Для получения дополнительной информации смотрите [в домене Azure AD Domain Domain Services, управляемом AD.](manage-dns.md)

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Что из себя представляет политика времени существования пароля в управляемом домене?
Время существования пароля по умолчанию в управляемом домене доменных служб Azure AD составляет 90 дней. Время существования этого пароля не синхронизировано со временем жизни пароля, настроенным в Azure AD. Таким образом может возникнуть ситуация, когда срок действия паролей пользователей заканчивается в управляемом домене, но они все еще действуют в Azure AD. В таких случаях пользователям требуется сменить пароль в Azure AD, после чего новый пароль синхронизируется с управляемым доменом. Кроме того, атрибуты *пароля-не-не-истечения срока* действия и атрибуты *пользователя-обязательно-изменения-пароля-на-следующем-logon* для учетных записей потребителя не синхронизированы с вашим управляемым доменом.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Предоставляют ли доменные службы Azure AD защиту учетной записи AD с помощью блокировки?
Да. При пяти недействительных попытках ввода пароля в течение 2 минут в управляемом домене происходит блокировка учетной записи пользователя на 30 минут. Через 30 минут учетная запись пользователя будет автоматически разблокирована. Попытки недействительных паролей в управляемом домене не блокируют учетную запись пользователя в Azure AD. Учетная запись пользователя заблокирована только в управляемом домене доменных служб Azure AD. Для получения дополнительной [информации см.](password-policy.md)

## <a name="billing-and-availability"></a>Выставление счетов и доступность

* [Являются ли доменные службы Azure AD платной службой?](#is-azure-ad-domain-services-a-paid-service)
* [Существует ли бесплатная пробная версия для службы?](#is-there-a-free-trial-for-the-service)
* [Можно ли приостановить управляемый домен доменных служб Azure AD?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Можно ли выполнить отработку отказа доменных служб Azure AD с переходом в другой регион при аварийном восстановлении?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Могу ли я получить услуги домена Azure AD в рамках пакета корпоративных мобильностей (EMS)? Нужен ли мне Azure AD Premium для использования служб доменов Azure AD?](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [В каких регионах Azure доступна служба?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Являются ли доменные службы Azure AD платной службой?
Да. Для получения дополнительной информации смотрите [страницу ценообразования](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Существует ли бесплатная пробная версия для службы?
Службы домена Azure AD включены в бесплатную пробную версию Для Azure. Вы можете зарегистрировать [бесплатную пробную версию Azure на один месяц](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Можно ли приостановить управляемый домен доменных служб Azure AD?
Нет. После включения домена Azure AD Domain Services, управляемого доменом, служба доступна в выбранной виртуальной сети до тех пор, пока вы не удалите управляемый домен. Нет никакого способа приостановить службу. Счета выставляются на почасовой основе, пока вы не удалите управляемый домен.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Можно ли выполнить отработку отказа доменных служб Azure AD с переходом в другой регион при аварийном восстановлении?
Нет. Службы домена Azure AD в настоящее время не предоставляют гео-излишние модели развертывания. Она ограничена одной виртуальной сетью в регионе Azure. Чтобы использовать несколько регионов Azure, необходимо запустить контроллеры домена Active Directory на виртуальных машинах IaaS Azure. Для руководства по архитектуре [см.](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Можно ли получить доменные службы Azure AD в составе Enterprise Mobility Suite (EMS)? Требуется ли Azure AD Premium для использования доменных служб Azure AD?
Нет. Служба домена Azure AD — это служба с оплатой по мере работы Azure и не является частью EMS. Службы домена Azure AD можно использовать во всех выпусках Azure AD (бесплатные и премиум). Вы выставляете счет на почасовой основе, в зависимости от использования.

### <a name="what-azure-regions-is-the-service-available-in"></a>В каких регионах Azure доступна служба?
Перейдите на страницу [служб Azure по регионам](https://azure.microsoft.com/regions/#services/), чтобы просмотреть список регионов Azure, в которых доступны доменные службы Azure AD.

## <a name="troubleshooting"></a>Устранение неполадок

Ознакомьтесь с нашим [руководством по устранению неполадок](troubleshoot.md) для решения распространенных проблем при настройке или администрировании доменных служб Azure AD.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше об службах домена Azure AD, [см.](overview.md)

Для начала см. Создать и настроить экземпляр службы [домена Active Directory Azure.](tutorial-create-instance.md)
