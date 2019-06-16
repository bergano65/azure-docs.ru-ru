---
title: Устранение неполадок с защищенным протоколом LDAP (LDAPS) в доменных службах Azure AD | Документация Майкрософт
description: Устранение неполадок с защищенным протоколом LDAP (LDAPS) для управляемого домена доменных служб Azure AD
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
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
ms.author: mstephen
ms.openlocfilehash: c9732545e1759ea23d62c0a56379e3868ed40a0b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246634"
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
* [Приступая к работе с доменными службами Azure AD](create-instance.md)
* [Управление домен доменных служб Azure AD](manage-domain.md)
* [LDAP Query Basics](https://technet.microsoft.com/library/aa996205.aspx) (Основы запроса LDAP)
* [Управление групповой политики для доменных служб Azure AD](manage-group-policy.md)
* [Группы безопасности сети](../virtual-network/security-overview.md)
* [Создание группы безопасности сети](../virtual-network/tutorial-filter-network-traffic.md)
