---
title: On-premises password protection FAQ - Azure Active Directory
description: Вопросы и ответы о локальной службе защиты паролем Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ee5d6328c6a3e4ea0b4a6359d4a21494e3ae62c
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381705"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Часто задаваемые вопросы о локальной службе защиты паролем Azure AD

This section provides answers to many commonly asked questions about Azure AD Password Protection.

## <a name="general-questions"></a>Общие вопросы

**Q: What guidance should users be given on how to select a secure password?**

В настоящее время это руководство Майкрософт можно найти по следующей ссылке:

[Руководство по паролям](https://www.microsoft.com/research/publication/password-guidance)

**Q: Is on-premises Azure AD Password Protection supported in non-public clouds?**

Нет. Локальная служба защиты паролем Azure AD поддерживается только в общедоступных облаках. Дата доступности в необщедоступных облаках еще не объявлена.

The Azure AD portal does allow modification of the on-premises-specific "Password protection for Windows Server Active Directory" configuration even in non-public clouds; such changes will be persisted but otherwise will never take effect. Registration of on-premises proxy agents or forests is unsupported when non-public cloud credentials are used, and any such registration attempts will always fail.

**Q: How can I apply Azure AD Password Protection benefits to a subset of my on-premises users?**

Не поддерживается. После развертывания и включения служба защиты паролей Azure AD не разделяет пользователей — все пользователи получают одинаковые преимущества безопасности.

**Q: What is the difference between a password change and a password set (or reset)?**

A password change is when a user chooses a new password after proving they have knowledge of the old password. For example, a password change is what happens when a user logs into Windows and is then prompted to choose a new password.

A password set (sometimes called a password reset) is when an administrator replaces the password on an account with a new password, for example by using the Active Directory Users and Computers management tool. This operation requires a high level of privilege (usually Domain Admin), and the person performing the operation usually does not have knowledge of the old password. Help-desk scenarios often perform password sets, for instance when assisting a user who has forgotten their password. You will also see password set events when a brand new user account is being created for the first time with a password.

The password validation policy behaves the same regardless of whether a password change or set is being done. The Azure AD Password Protection DC Agent service does log different events to inform you whether a password change or set operation was done.  See [Azure AD Password Protection monitoring and logging](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**Q: Why are duplicated password rejection events logged when attempting to set a weak password using the Active Directory Users and Computers management snap-in?**

The Active Directory Users and Computers management snap-in will first try to set the new password using the Kerberos protocol. Upon failure, the snap-in will make a second attempt to set the password using a legacy (SAM RPC) protocol (the specific protocols used are not important). If the new password is considered weak by Azure AD Password Protection, this snap-in behavior will result in two sets of password reset rejection events being logged.

**Q: Why are Azure AD Password Protection password validation events being logged with an empty user name?**

Active Directory supports the ability to test a password to see if it passes the domain's current password complexity requirements, for example using the [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) api. When a password is validated in this way, the testing also includes validation by password-filter-dll based products such as Azure AD Password Protection - but the user names passed to a given password filter dll will be empty. In this scenario, Azure AD Password Protection will still validate the password using the currently in-effect password policy and will issue an event log message to capture the outcome, however the event log message will have empty user name fields.

**Q: Is it supported to install Azure AD Password Protection side by side with other password-filter-based products?**

Да. Поддержка нескольких зарегистрированных библиотек DLL для фильтрации по паролю является основной функцией Windows и не относится к защите паролем Azure AD. Все зарегистрированные библиотеки DLL для фильтрации по паролю должны подтвердить пароль, прежде чем он будет принят.

**Q: How can I deploy and configure Azure AD Password Protection in my Active Directory environment without using Azure?**

Не поддерживается. Защита паролей Azure AD — это компонент Azure, который поддерживает расширение в локальную среду Active Directory.

**Q: How can I modify the contents of the policy at the Active Directory level?**

Не поддерживается. The policy can only be administered using the Azure AD portal. См. также предыдущий вопрос.

**Вопрос. Почему для репликации SYSVOL требуется DFSR?**

Технология FRS (предшествующая технологии DFSR) имеет множество известных проблем и полностью не поддерживается в новых версиях Windows Server Active Directory. Ноль-тестирование защиты паролем Azure AD будет выполняться на доменах, для которых настроена FRS.

Дополнительные сведения приведены в следующих статьях:

[The Case for Migrating sysvol replication to DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr) (Вариант переноса репликации SYSVOL в DFSR)

[The End is Nigh for FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs) (Близится конец FRS)

If your domain is not already using DFSR, you MUST migrate it to use DFSR before installing Azure AD Password Protection. For more information, see the following link:

[SYSVOL Replication Migration Guide: FRS to DFS Replication](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> The Azure AD Password Protection DC Agent software will currently install on domain controllers in domains that are still using FRS for sysvol replication, but the software will NOT work properly in this environment. Additional negative side-effects include individual files failing to replicate, and sysvol restore procedures appearing to succeed but silently failing to replicate all files. You should migrate your domain to use DFSR as soon as possible, both for DFSR's inherent benefits and also to unblock the deployment of Azure AD Password Protection. Future versions of the software will be automatically disabled when running in a domain that is still using FRS.

**Q: How much disk space does the feature require on the domain sysvol share?**

Точное использование пространства зависит от таких факторов, как количество и длина запрещенных маркеров в глобальном списке запрещенных маркеров Майкрософт и настраиваемом списке каждого клиента, а также накладные расходы на шифрование. Эти списки, вероятно, будут расширяться в будущем. Учитывая это, разумно ожидать, что для этого компонента потребуется по крайней мере пять (5) мегабайт пространства в общей папке sysvol домена.

**Вопрос. Почему для установки или обновления программного обеспечения агента контроллера домена требуется перезагрузка?**

Это требование связано с ключевым механизмом Windows.

**В. Имеется ли способ настроить агент контроллера домена для использования конкретного прокси-сервера?**

Нет. Так как прокси-сервер не учитывает состояние, не важно, какой конкретный прокси-сервер используется.

**Q: Is it okay to deploy the Azure AD Password Protection Proxy service side by side with other services such as Azure AD Connect?**

Да. Служба прокси-сервера Защиты паролей Azure AD и Azure AD Connect никогда не конфликтуют друг с другом напрямую.

Unfortunately, an incompatibility has been found between the version of the Microsoft Azure AD Connect Agent Updater service that is installed by the Azure AD Password Protection Proxy software and the version of the service that is installed by the [Azure Active Directory Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) software. This incompatibility may result in the Agent Updater service being unable to contact Azure for software updates. It is not recommended to install Azure AD Password Protection Proxy and Azure Active Directory Application Proxy on the same machine.

**Q: In what order should the DC agents and proxies be installed and registered?**

Any ordering of Proxy agent installation, DC agent installation, forest registration, and Proxy registration  is supported.

**Q: Should I be concerned about the performance hit on my domain controllers from deploying this feature?**

Служба агента контроллера домена Защиты паролей Azure AD не должна существенно влиять на производительность контроллера домена в существующем работоспособном развертывании Active Directory.

Для большинства развертываний Active Directory операции изменения пароля составляют небольшую часть общей рабочей нагрузки на любой контроллер домена. Например, представьте домен Active Directory с 10 000 учетных записей пользователей и политикой MaxPasswordAge со значением 30 дней. В среднем на этом домене будет выполняться 10 000/30=~333 операции смены паролей каждый день, что является незначительным количеством операций даже для одного контроллера домена. Рассмотрим потенциальный наихудший сценарий: предположим, что эти ~333 смены пароля на одном контроллере домена произошли за один час. Например, этот сценарий может возникнуть, когда много сотрудников приходят на работу утром в понедельник. Даже в этом случае мы по-прежнему получаем ~333/60 минут = шесть изменений пароля в минуту, что опять же не является значительной нагрузкой.

Тем не менее, если текущие контроллеры домена уже работают на уровнях с ограниченной производительностью (например, по максимуму используют ЦП, дисковое пространство, дисковые операции ввода-вывода и т. д.), рекомендуется добавить дополнительные контроллеры домена или расширить доступное дисковое пространство перед развертыванием этого компонента. См. также вопрос об использовании дискового пространства sysvol выше.

**Q: I want to test Azure AD Password Protection on just a few DCs in my domain. Is it possible to force user password changes to use those specific DCs?**

Нет. Клиентская ОС Windows определяет, какой контроллер домена используется при изменении пароля пользователем. The domain controller is selected based on factors such as Active Directory site and subnet assignments, environment-specific network configuration, etc. Azure AD Password Protection does not control these factors and cannot influence which domain controller is selected to change a user's password.

Один из способов частично достичь этой цели — развернуть Защиту паролей Azure AD на всех контроллерах домена на данном сайте Active Directory. Такой подход обеспечит разумное покрытие для клиентов Windows, назначенных этому сайту, а также для пользователей, которые входят в эти клиенты и меняют свои пароли.

**Q: If I install the Azure AD Password Protection DC Agent service on just the Primary Domain Controller (PDC), will all other domain controllers in the domain also be protected?**

Нет. Когда пароль пользователя изменяется на контроллере домена, отличном от основного, открытый пароль никогда не отправляется на основной контроллер домена (эта идея является распространенным заблуждением). Когда новый пароль будет принят на данном контроллере домена, этот контроллер домена начнет использовать его, чтобы создавать различные хэши этого пароля для конкретного протокола аутентификации, а затем сохранять их в каталоге. Открытый пароль не сохраняется. Затем обновленные хэши реплицируются в этот основной контроллер домена. В некоторых случаях пароли пользователей могут быть изменены непосредственно на основном контроллере домена в зависимости от различных факторов, таких как топология сети и структура сайта Active Directory. (См. предыдущий вопрос.)

Таким образом, развертывание службы агента контроллера домена Защиты паролей Azure AD только на основном контроллере домена необходимо для достижения 100%-го уровня безопасности компонента в домене. Развертывание компонента только на основном контроллере домена не предоставляет преимуществ Защиты паролей Azure AD для других контроллеров в домене.

**Q: Why is custom smart lockout not working even after the agents are installed in my on-premises Active Directory environment?**

Custom smart lockout is only supported in Azure AD. Changes to the custom smart lockout settings in the Azure AD portal have no effect on the on-premises Active Directory environment, even with the agents installed.

**Q: Is a System Center Operations Manager management pack available for Azure AD Password Protection?**

Нет.

**Q: Why is Azure AD still rejecting weak passwords even though I've configured the policy to be in Audit mode?**

Audit mode is only supported in the on-premises Active Directory environment. Azure AD is implicitly always in "enforce" mode when it evaluates passwords.

**Q: My users see the traditional Windows error message when a password is rejected by Azure AD Password Protection. Is it possible to customize this error message so that users know what really happened?**

Нет. The error message seen by users when a password is rejected by a domain controller is controlled by the client machine, not by the domain controller. This behavior happens whether a password is rejected by the default Active Directory password policies or by a password-filter-based solution such as Azure AD Password Protection.

## <a name="additional-content"></a>Дополнительное содержимое

Следующие ссылки не являются частью основной документации по Защите паролей Azure AD, но могут быть полезным источником дополнительных сведений о компоненте.

[Azure AD Password Protection is now generally available!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Email Phishing Protection Guide – Part 15: Implement the Microsoft Azure AD Password Protection Service (for On-Premises too!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD Password Protection and Smart Lockout are now in Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529) (Компонент "Защита паролей Azure AD" и интеллектуальная блокировка теперь предоставляются в общедоступной версии)

## <a name="microsoft-premierunified-support-training-available"></a>Доступно обучение по поддержке Microsoft Premier и Единой поддержке Майкрософт

Если вы хотите узнать больше о Защите паролей Azure AD и развертывании этого компонента в своей среде, вы можете воспользоваться проактивной службой Майкрософт, доступной для тех клиентов, которые заключили контракт на поддержку Premier или Единую поддержку. The service is called Azure Active Directory: Password Protection. Свяжитесь со своим техническим менеджером по работе с клиентами, чтобы получить дополнительные сведения.

## <a name="next-steps"></a>Дальнейшие действия

Если у вас есть вопрос о локальной службе защиты паролей Azure AD, который не освещается в этой статье, ниже вы можете отправить нам отзыв. Спасибо!

[Развертывание защиты паролем Azure AD](howto-password-ban-bad-on-premises-deploy.md)
