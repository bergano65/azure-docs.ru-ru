---
title: Устранение неполадок динамического членства в группах | Документация Майкрософт
description: Советы по устранению неполадок динамического членства в группах в Azure AD.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: ab5a3b00d063dfdd42e67247bb2cdc37866d0164
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164135"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Устранение неполадок, связанных с динамическим членством в группах

**Мной было настроено правило для группы, но обновление членства в группе не произошло.**<br/>Проверьте значения атрибутов пользователей в правиле — существуют ли пользователи, удовлетворяющие правилу? Если все правильно, подождите некоторое время, пока не заполнится группа. В зависимости от размера клиента, первоначальное заполнение группы или ее заполнение после изменения правила может занять до 24 часов.

**Мной было настроено правило, но теперь существующие участники правила удалены.**<br/>Это ожидаемое поведение. Существующие участники группы удаляются при включении или изменении правила. В группу добавляются пользователи, подобранные на основе оценки правила.

**Я не вижу мгновенного изменения членства, когда добавляю или изменяю правило. Почему?**<br/>Специальная оценка членства выполняется периодически в асинхронном фоновом процессе. На длительность процесса влияют количество пользователей в каталоге и размер группы, созданной в результате оценки правила. Обычно каталоги с небольшим количеством пользователей выявят изменения членства в группах уже через несколько минут. В случае каталогов с большим числом пользователей заполнение может занять 30 минут или больше.

**У меня возникла ошибка при обработке правила**<br/>В следующей таблице перечислены распространенные ошибки с правилом динамического членства и способы их исправления.

| Ошибка со средством синтаксического анализа правил | Неправильное использование | Правильное использование |
| --- | --- | --- |
| Ошибка: Атрибут не поддерживается. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>Убедитесь, что атрибут находится в [списке поддерживаемых свойств](groups-dynamic-membership.md#supported-properties). |
| Ошибка: Не поддерживается оператор для атрибута. |(user.accountEnabled -contains true) |(user.accountEnabled - eq true)<br/><br/>Используемый оператор не поддерживается для этого типа свойств (в данном примере -contains не может использоваться в логическом типе). Используйте правильные операторы для типа свойств. |
| Ошибка: Ошибка компиляции запроса. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "*@domain.ext") | 1. Отсутствует оператор. Используйте предикат соединения -and или -or.<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. Ошибка в регулярном выражении, используемом с -match.<br>(user.userPrincipalName -match ".*@domain.ext")<br>или (user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>Дополнительная информация

В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Управление доступом к ресурсам с помощью групп Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Управление приложениями в Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Что такое Microsoft Azure Active Directory](../fundamentals/active-directory-whatis.md)
* [Интеграция локальных удостоверений с Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
