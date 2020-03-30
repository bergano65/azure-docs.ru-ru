---
title: Устранение проблем в безопасности LDAP в службах домена Azure AD (ru) Документы Майкрософт
description: Узнайте, как устранить проблемы безопасной LDAP (LDAPS) для домена идочебных служб active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132165"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Устранение проблем обеспечивает безопасность проблем подключения LDAP к домену управления идоменом Active Directory Domain

Приложения и службы, которые используют легкий протокол доступа к каталогам (LDAP) для связи с службами домена Azure Active Directory Domain Services (Azure AD DS), могут быть [настроены на безопасное LDAP.](tutorial-configure-ldaps.md) Соответствующий сертификат и необходимые сетевые порты должны быть открыты для правильной работы LDAP.

Эта статья помогает устранить проблемы с защищенным доступом к LDAP в Azure AD DS.

## <a name="common-connection-issues"></a>Общие проблемы подключения

Если у вас возникли проблемы с подключением к домену Azure AD DS с помощью защищенного LDAP, просмотрите следующие шаги по устранению неполадок. После каждого шага по устранению неполадок попробуйте снова подключиться к домену Azure AD DS:

* Цепочка издателей сертификата защищенного протокола LDAP должна быть доверенной в клиенте. Вы можете добавить орган по сертификации Root (CA) в доверенный магазин корневых сертификатов на клиента, чтобы установить траст.
    * Убедитесь, что вы [экспортируете и применяете сертификат на клиентские компьютеры.][client-cert]
* Проверка защищенного сертификата LDAP для управляемого домена имеет имя DNS в *субъекте* или атрибут *«Альтернативные имена субъекта».*
    * Просмотрите [требования к защищенным сертификатам LDAP][certs-prereqs] и при необходимости создайте сертификат замены.
* Убедитесь, что клиент LDAP, например *ldp.exe,* подключается к безопасной конечной точке LDAP, используя имя DNS, а не IP-адрес.
    * Сертификат, применяемый к домену Azure AD DS, не включает IP-адреса службы, а только имена DNS.
* Проверьте имя DNS, к которому подключается клиент LDAP. Он должен решить с общедоступным IP-адресом для безопасного LDAP в домене Azure AD DS.
    * Если имя DNS разрешится с внутренним IP-адресом, обновите запись DNS для решения внешнего IP-адреса.
* Для внешнего подключения группа сетевой безопасности должна включать правило, позволяющее трафик в порт TCP 636 из Интернета.
    * Если вы можете подключиться к управляемому домену Azure AD DS с помощью защищенного LDAP из ресурсов, непосредственно подключенных к виртуальной сети, но не внешних соединений, убедитесь, что вы [создаете правило группы безопасности сети, чтобы обеспечить безопасность трафика LDAP.][ldaps-nsg]

## <a name="next-steps"></a>Дальнейшие действия

Если у вас все еще есть проблемы, [откройте запрос службы поддержки Azure][azure-support] для дополнительной помощи в устранении неполадок.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
