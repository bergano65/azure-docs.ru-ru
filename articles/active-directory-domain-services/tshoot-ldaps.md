---
title: Устранение неполадок с защищенным протоколом LDAP (LDAPS) в доменных службах Azure AD | Документация Майкрософт
description: Устранение неполадок с защищенным протоколом LDAP (LDAPS) для управляемого домена доменных служб Azure AD
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 285af0e5e5d5ab03027fc29064a5f3623ed10e2f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617048"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Устранение неполадок с защищенным протоколом LDAP (LDAPS) для управляемого домена доменных служб Azure AD

## <a name="connection-issues"></a>Проблемы с подключением
При возникновении проблем с подключением к управляемому домену с использованием защищенного протокола LDAP сделайте следующее:

* Цепочка издателей сертификата защищенного протокола LDAP должна быть доверенной в клиенте. Для установления отношения доверия можно добавить корневой центр сертификации в хранилище доверенных корневых сертификатов на клиенте.
* Убедитесь, что клиент LDAP (например, ldp.exe) подключается к конечной точке защищенного протокола LDAP с помощью DNS-имени, а не IP-адреса.
* Проверьте имя DNS, к которому подключается клиент LDAP. Оно должно разрешаться в общедоступный IP-адрес для защищенного протокола LDAP в управляемом домене.
* Убедитесь, что сертификат защищенного протокола LDAP для управляемого домена содержит DNS-имя в атрибуте "Субъект" или "Альтернативное имя субъекта" (SAN).
* Параметры NSG для виртуальной сети должны разрешать поступление трафика из Интернета на порт 636. Это действие применяется, только если включен защищенный доступ по протоколу LDAP через Интернет.


## <a name="need-help"></a>Требуется помощь?
Если проблема с подключением к управляемому домену с использованием защищенного протокола LDAP сохранится, то [обратитесь за помощью к команде разработчиков](contact-us.md). Сообщите им приведенные ниже сведения, чтобы помочь с диагностикой проблемы.
* Снимок экрана, на котором ldp.exe пытается подключиться, но попытка завершается сбоем.
* Ваш идентификатор клиента Azure AD и доменное имя DNS вашего управляемого домена.
* Точное имя пользователя, которое вы пытаетесь привязать.


## <a name="related-content"></a>Связанная информация
* [Приступая к работе с доменными службами Azure AD](tutorial-create-instance.md)
* [Управление доменом доменных служб Azure AD](tutorial-create-management-vm.md)
* [LDAP Query Basics](https://technet.microsoft.com/library/aa996205.aspx) (Основы запроса LDAP)
* [Управление групповая политика для доменных служб Azure AD](manage-group-policy.md)
* [Группы безопасности сети](../virtual-network/security-overview.md)
* [Создание группы безопасности сети](../virtual-network/tutorial-filter-network-traffic.md)
