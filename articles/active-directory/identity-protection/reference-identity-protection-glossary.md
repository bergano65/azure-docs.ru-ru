---
title: Глоссарий по защите идентификации Azure Active Directory
description: Глоссарий по защите идентификации Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232344"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Глоссарий по защите идентификации Azure Active Directory

### <a name="at-risk-user"></a>Под угрозой (пользователь)
A user with one or more active risk detections. 

### <a name="atypical-sign-in-location"></a>Нетипичное расположение входа
Вход из географического расположения, которое не является типичным для конкретного пользователя, схожих пользователей или клиента.

### <a name="azure-ad-identity-protection"></a>защиту идентификации Azure AD
A security module of Azure Active Directory that provides a consolidated view into risk detections and potential vulnerabilities affecting an organization’s identities.

### <a name="conditional-access"></a>Условный доступ
Политика для защиты доступа к ресурсам. Conditional Access rules are stored in the Azure Active Directory and are evaluated by Azure AD before granting access to the resource.  Примерами правил являются ограничения доступа на основе расположения пользователя, работоспособности устройства или метода проверки подлинности пользователя.

### <a name="credentials"></a>Учетные данные
Данные, которые включают в себя идентификацию и проверку идентификации, используемой для получения доступа к локальным и сетевым ресурсам. Примерами учетных данных являются имена пользователей и пароли, смарт-карты и сертификаты.

### <a name="event"></a>Мероприятие
Запись действия в Azure Active Directory.

### <a name="false-positive-risk-detection"></a>False-positive (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection was investigated and was incorrectly flagged as a risk detection.

### <a name="identity"></a>Удостоверение
Лицо или организация, которые должны быть проверены с помощью средств проверки подлинности на основе определенных условий, таких как пароль или сертификат.

### <a name="identity-risk-detection"></a>Identity risk detection
Azure AD event that was flagged as anomalous by Identity Protection, and may indicate that an identity has been compromised.

### <a name="ignored-risk-detection"></a>Ignored (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection is closed without taking a remediation action.

### <a name="impossible-travel-from-atypical-locations"></a>Невозможно переместиться из нетипичных расположений
A risk detection triggered when two sign-ins for the same user are detected, where at least one of them is from an atypical sign-in location, and where the time between the sign-ins is shorter than the minimum time it would take to physically travel between these locations.  

### <a name="investigation"></a>Исследование
The process of reviewing the activities, logs, and other relevant information related to a risk detection to decide whether remediation or mitigation steps are necessary, understand if and how the identity was compromised, and understand how the compromised identity was used.

### <a name="leaked-credentials"></a>Утерянные учетные данные
A risk detection triggered when current user credentials (user name and password) are found posted publicly in the Dark   web by our researchers.

### <a name="mitigation"></a>Устранение
Действие, позволяющее ограничить или исключить возможность использования злоумышленником скомпрометированного удостоверения или устройства без восстановления их безопасного состояния. A mitigation does not resolve previous risk detections associated with the identity or device.

### <a name="multi-factor-authentication"></a>Многофакторная Идентификация
Метод проверки подлинности, требующий двух или более способов проверки подлинности, включая следующее: предоставляемые пользователем объекты, например сертификаты; известные только пользователям сведения, например имена пользователей, пароли или парольные фразы; физические атрибуты, например отпечаток большого пальца; персональные атрибуты, например личная подпись.

### <a name="offline-detection"></a>Обнаружение в автономном режиме
Обнаружение аномалий и оценки риска события, такого как попытка входа после того, как событие уже возникло.

### <a name="policy-condition"></a>Условие политики
Часть политики безопасности, которая определяет сущности (группы, пользователи, приложения, платформы устройств, состояния устройств, диапазоны IP-адресов, типы клиентов), включаемые в политику или исключаемые из нее.

### <a name="policy-rule"></a>Правило политики
The part of a security policy that describes the circumstances that would trigger the policy, and the actions taken when the policy is triggered.

### <a name="prevention"></a>Предотвращение
Действие для предотвращения нанесения ущерба организации путем использования удостоверения или устройства, которое ранее предположительно или фактически было скомпрометировано. A prevention action does not secure the device or identity, and does not resolve previous risk detections.

### <a name="privileged-user"></a>Привилегированный (пользователь)
A user that at the time of a risk detection, had permanent or temporary admin permissions to one or more resources in Azure Active Directory, such as a Global Administrator, Billing Administrator, Service Administrator, User administrator, and Password Administrator. 

### <a name="real-time"></a>В режиме реального времени
См. "Обнаружение в режиме реального времени".

### <a name="real-time-detection"></a>Обнаружение в режиме реального времени.
Обнаружение аномалий и оценки риска события, такого как попытка входа до разрешения продолжения события.

### <a name="remediated-risk-detection"></a>Remediated (risk detection)
A risk detection status set automatically by Identity Protection, indicating that the risk detection was remediated using the standard remediation action for this type of risk detection. For example, when the user password is reset, many risk detections that indicate that the previous password was compromised are automatically remediated.

### <a name="remediation"></a>Исправление
Действие для защиты удостоверения или устройства, которое было ранее предположительно или фактически скомпрометировано. A remediation action restores the identity or device to a safe state, and resolves previous risk detections associated with the identity or device.

### <a name="resolved-risk-detection"></a>Resolved (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the user took an appropriate remediation action outside Identity Protection, and that the risk detection should be considered closed.

### <a name="risk-detection-status"></a>Risk detection status
A property of a risk detection, indicating whether the event is active, and if closed, the reason for closing it.

### <a name="risk-detection-type"></a>Тип обнаружения риска
A category for the risk detection, indicating the type of anomaly that caused the event to be considered risky.

### <a name="risk-level-risk-detection"></a>Risk level (risk detection)
An indication (High, Medium, or Low) of the severity of the risk detection to help Identity Protection users prioritize the actions they take to reduce the risk to their organization. 

### <a name="risk-level-sign-in"></a>Уровень риска (вход)
Обозначение уровня вероятности ("Высокий", "Средний" или "Низкий") того, что при конкретной попытке входа удостоверение пользователя будет использоваться другим лицом.

### <a name="risk-level-user-compromise"></a>Уровень риска (компрометация пользователя)
Обозначение уровня вероятности ("Высокий", "Средний" или "Низкий") компрометации удостоверения.

### <a name="risk-level-vulnerability"></a>Уровень риска (уязвимость)
Обозначение уровня серьезности уязвимости ("Высокий", "Средний" или "Низкий"), чтобы помочь пользователям модуля защиты идентификации расставить приоритеты действий, которые необходимо предпринять, чтобы уменьшить риск для организации.

### <a name="secure-identity"></a>Безопасное (удостоверение)
Выполнение действия исправления, например смена пароля или повторное создание образа компьютера, для восстановления безопасного состояния скомпрометированного удостоверения.

### <a name="security-policy"></a>Политика безопасности
Коллекция правил и условий политики. Политика может применяться к сущностям, таким как пользователи, группы, приложения, устройства, платформы устройств, состояния устройств, диапазоны IP-адресов и типы клиентов Auth2.0. После включения политика проходит оценку каждый раз, когда включенной в политику сущности выдается маркер для ресурса.

### <a name="sign-in-v"></a>Выполнить вход
Проверить подлинность удостоверения в Azure Active Directory.

### <a name="sign-in-n"></a>Вход
Процесс или действие проверки подлинности удостоверения в Azure Active Directory и событие, которое записывает эту операцию.

### <a name="sign-in-from-anonymous-ip-address"></a>Вход с анонимных IP-адресов
A risk detection triggered after a successful sign-in from IP address that has been identified as an anonymous proxy IP address.

### <a name="sign-in-from-infected-device"></a>Вход с инфицированных устройств
A risk detection triggered when a sign-in originates from an IP address, which is known to be used by one or more compromised devices, which are actively attempting to communicate with a bot server.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Вход с IP-адресов с подозрительными действиями
A risk detection triggered after a successful sign-in from an IP address with a high number of failed login attempts across multiple user accounts over a short period of time.

### <a name="sign-in-from-unfamiliar-location"></a>Вход из неизвестного расположения
A risk detection triggered when a user successfully signs in from a new location (IP, Latitude/Longitude, and ASN).

### <a name="sign-in-risk"></a>Риск при входе
См. "Уровень риска" (вход)

### <a name="sign-in-risk-policy"></a>Политика риска входа
A Conditional Access policy that evaluates the risk to a specific sign-in and applies mitigations based on predefined conditions and rules.

### <a name="user-compromise-risk"></a>Риск компрометации пользователя
См. "Уровень риска" (компрометация пользователя)

### <a name="user-risk"></a>Риск пользователя
См. "Уровень риска" (компрометация пользователя)

### <a name="user-risk-policy"></a>Политика риска пользователя
A Conditional Access policy that considers the sign-in and applies mitigations based on predefined conditions and rules.

### <a name="users-flagged-for-risk"></a>Пользователи, помеченные для события риска
Users that have risk detections, which are either active or remediated

### <a name="vulnerability"></a>Уязвимость
Конфигурация или условие в Azure Active Directory, которое делает каталог уязвимым к атакам или угрозам.

## <a name="see-also"></a>Дополнительные материалы

- [Защита идентификации Azure Active Directory.](../active-directory-identityprotection.md)
