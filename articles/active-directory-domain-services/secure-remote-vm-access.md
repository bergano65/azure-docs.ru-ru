---
title: Безопасный удаленный доступ к VM в службах домена Azure AD Domain Services Документы Майкрософт
description: Узнайте, как обеспечить удаленный доступ к vpn с помощью сервера сетевой политики (NPS) и Multi-Factor Authentication Azure с развертыванием удаленных настольных служб в домене Active Directory Domain.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 8bc36dfdf3010b2bde485228f6ee110b0b826d31
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654747"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Безопасный удаленный доступ к виртуальным машинам в службах домена Active Directory

Для обеспечения удаленного доступа к виртуальным машинам (Виртуальным машинам), работающим в домене Azure Active Directory Domain Services (Azure AD DS), можно использовать службы удаленного рабочего стола (RDS) и сервер сетевой политики (NPS). Azure AD DS проверяет подлинность пользователей при запросе доступа через среду RDS. Для повышения безопасности можно интегрировать многофакторную аутентификацию Azure, чтобы обеспечить дополнительную подсказку аутентификации во время событий, входящих в систему. Для предоставления этой функции Azure Multi-Factor Authentication использует расширение для NPS.

> [!IMPORTANT]
> Рекомендуемый способ безопасного подключения к виртуальным в домене Azure AD DS — это использование Azure Bastion, полностью управляемого платформой сервиса PaaS, который вы предоставляете внутри виртуальной сети. Узел бастиона обеспечивает безопасное и бесшовное подключение удаленного настольного протокола (RDP) к вашим всасываниям непосредственно на портале Azure через SSL. При подключении через услик бастиона вашим визам не нужен общедоступный IP-адрес, и вам не нужно использовать группы сетевой безопасности для обеспечения доступа к RDP в порту TCP 3389.
>
> Мы настоятельно рекомендуем использовать Azure Bastion во всех регионах, где он поддерживается. В регионах, где нет доступа к Azure Bastion, следуйте шагам, описанным в этой статье, пока не будет доступна Azure Bastion. Позаботьтесь о назначении общедоступных IP-адресов в адрес ВМ, объединенных в Azure AD DS, где разрешен весь входящий трафик RDP.
>
> Для получения дополнительной информации, [см. Что такое Azure Бастион?][bastion-overview].

В этой статье показано, как настроить RDS в Azure AD DS и дополнительно использовать расширение NPS мультифакторной аутентификации Azure Multi-Factor.

![Обзор удаленных настольных служб (RDS)](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Предварительные требования

Для завершения этой статьи необходимы следующие ресурсы:

* Активная подписка Azure.
    * Если у вас нет подписки НаAz, [создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * Если потребуется, [создайте и настройте экземпляр доменных служб Azure Active Directory][create-azure-ad-ds-instance].
* Подсеть *рабочих нагрузок,* созданная в виртуальной сети Доменных служб Активных Каталогов Azure.
    * При необходимости [направите виртуальную сеть для домена Azure Active Directory Domain Domain.][configure-azureadds-vnet]
* Учетная запись пользователя, входящая в группу *администраторов Azure AD DC* в клиенте Azure AD.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Развертывание и настройка среды удаленного рабочего стола

Чтобы начать работу, создайте как минимум два VMs Azure, которые работают под Windows Server 2016 или Windows Server 2019. Для избыточности и высокой доступности среды удаленного рабочего стола (RD) можно добавить и загрузить баланс дополнительных узлов позже.

Предлагаемое развертывание RDS включает в себя следующие два ВМ:

* *RDGVM01* - запускает сервер RD Connection Broker, RD Web Access server и RD Gateway.
* *RDSHVM01* - запускает сервер RD Session Host.

Убедитесь, что виртуальные вывеши развернуты в подсети *рабочих нагрузок* виртуальной сети Azure AD DS, а затем присоединяйтесь к виртуальным визажам в домен Azure AD DS. Для получения дополнительной информации узнайте, как [создать и присоединиться к VM Windows Server в домене Azure AD DS.][tutorial-create-join-vm]

Развертывание среды RD содержит ряд шагов. Существующее руководство по развертыванию RD может быть использовано без каких-либо конкретных изменений для использования в домене Azure AD DS:

1. Восвашите в систему Для расчетов на встретимся с учетной записью, вкоторууутой в группе *администраторов Azure AD,* такой как *contosoadmin.*
1. Для создания и настройки RDS используйте существующее [руководство по развертыванию среды удаленного рабочего стола.][deploy-remote-desktop] Распределяйте компоненты RD-сервера по всем вашим VMs-серверам Azure по желанию.
1. Если вы хотите предоставить доступ с помощью веб-браузера, [навяжните веб-клиент Удаленного рабочего стола для пользователей.][rd-web-client]

При развертывании RD в домене Azure AD DS можно управлять и использовать службу, как это было бы с доменом AD DS.

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>Развертывание и настройка NPS и расширения NPS Azure MFA

Если вы хотите повысить безопасность работы пользователя, можно дополнительно интегрировать среду RD с многофакторной аутентификацией Azure Multi-Factor Authentication. С помощью этой конфигурации пользователи получают дополнительный запрос во время входной связи для подтверждения своей личности.

Для обеспечения этой возможности в вашей среде установлен дополнительный сервер сетевой политики (NPS) вместе с расширением NPS NPS многофакторной аутентификации Azure. Это расширение интегрируется с Azure AD для запроса и возврата статуса многофакторных запросов аутентификации.

Пользователи должны быть [зарегистрированы для использования Azure Multi-Фактор аутентификации][user-mfa-registration], которая может потребовать дополнительных лицензий Azure AD.

Для интеграции многофакторной аутентификации Azure в среду удаленного рабочего стола Azure AD DS создайте NPS Server и установите расширение:

1. Создайте дополнительный VM Windows Server 2016 или 2019, такой как *NPSVM01,* подключенный к подсети *рабочих нагрузок* в виртуальной сети Azure AD DS. Присоединяйтесь к VM в домене Azure AD DS, управляемый DS.
1. Восвай в NPS VM в качестве учетной записи, входят в группу *администраторов Azure AD DC,* например, *contosoadmin.*
1. Из **менеджера серверов**выберите **Добавить роли и функции,** а затем установите роль *сетевой политики и служб доступа.*
1. Используйте существующую статью для [установки и настройки расширения NpS Azure MFA.][nps-extension]

С установленным сервером NPS и расширением NPS Multi-Factor Authentication завершите следующий раздел, чтобы настроить его для использования в среде RD.

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>Интеграция удаленного настольного шлюза и многофакторной аутентификации Azure

Для интеграции расширения NPS мультифакторной аутентификации Azure используйте существующую статью «как-к» для [интеграции инфраструктуры удаленного рабочего стола с помощью расширения Network Policy Server (NPS) и Azure AD.][azure-mfa-nps-integration]

Для интеграции в управляемый домен Azure AD DS необходимы следующие дополнительные параметры конфигурации:

1. Не [регистрируйте сервер NPS в Active Directory.][register-nps-ad] Этот шаг выходит из строя в домене Azure AD DS, управляемом DS.
1. В [шаге 4 для настройки сетевой политики,][create-nps-policy]а также проверить окно **игнорировать пользовательский счет набора в свойствах.**
1. Если вы используете Windows Server 2019 для сервера NPS и расширения NPS-мультифакторной аутентификации Azure, запустите следующую команду для обновления безопасного канала, чтобы сервер NPS общался правильно:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Пользователям теперь предлагается дополнительный фактор аутентификации при входе в систему, например, текстовое сообщение или запрос в приложении Microsoft Authenticator.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации об улучшении устойчивости развертывания [см.][rds-high-availability]

Для получения дополнительной информации об обеспечении безопасности пользователя, см. [Как это работает: Azure Multi-фактор аутентификации][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-plan-high-availability
