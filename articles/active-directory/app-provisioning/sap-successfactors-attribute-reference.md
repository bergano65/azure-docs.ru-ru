---
title: Справочник по атрибутам SAP SuccessFactors
description: Сведения о том, какие атрибуты SuccessFactors поддерживаются при подготовке c помощью решения по управлению персоналом SuccessFactors.
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: 0d1a25f67d3550e79b2339ee060d37acaea0c1c4
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255548"
---
# <a name="sap-successfactors-attribute-reference"></a>Справочник по атрибутам SAP SuccessFactors

В этой статье вы найдете сведения о:

- [Сущности и атрибуты SuccessFactors](#supported-successfactors-entities-and-attributes)
- [Сопоставление атрибутов по умолчанию](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>Поддерживаемые сущности и атрибуты SuccessFactors

В следующей таблице приведен список атрибутов SuccessFactors, включаемых по умолчанию в следующие два приложения подготовки:

- [SuccessFactors Active Directory подготовки пользователей](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors для подготовки пользователей Azure AD](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

Сведения о расширении схемы для дополнительных атрибутов см. в [справочнике по интеграции SAP SuccessFactors](./sap-successfactors-integration-reference.md#retrieving-additional-attributes) . 

| \# | Сущность SuccessFactors                  | Атрибут SuccessFactors     | Тип операции |
|----|----------------------------------------|------------------------------|----------------|
| 1  | перперсон                              | personIdExternal             | Чтение           |
| 2  | перперсон                              | personId                     | Чтение           |
| 3  | перперсон                              | перперсонууид                | Чтение           |
| 4  | перперсонал                            | displayName                  | Чтение           |
| 5  | перперсонал                            | firstName                    | Чтение           |
| 6  | перперсонал                            | gender                       | Чтение           |
| 7  | перперсонал                            | lastName                     | Чтение           |
| 8  | перперсонал                            | middleName                   | Чтение           |
| 9  | перперсонал                            | преферреднаме                | Чтение           |
| 10 | User                                   | addressLine1                 | Чтение           |
| 11 | User                                   | addressLine2                 | Чтение           |
| 12 | Пользователь                                   | addressLIne3                 | Чтение           |
| 13 | User                                   | бусинессфоне                | Чтение           |
| 14 | User                                   | целлфоне                    | Чтение           |
| 15 | Пользователь                                   | city                         | Чтение           |
| 16 | Пользователь                                   | country                      | Чтение           |
| 17 | User                                   | custom01                     | Чтение           |
| 18 | User                                   | custom02                     | Чтение           |
| 19 | User                                   | custom03                     | Чтение           |
| 20 | User                                   | custom04                     | Чтение           |
| 21 | User                                   | custom05                     | Чтение           |
| 22 | User                                   | custom06                     | Чтение           |
| 23 | User                                   | custom07                     | Чтение           |
| 24 | User                                   | custom08                     | Чтение           |
| 25 | User                                   | custom09                     | Чтение           |
| 26 | User                                   | custom10                     | Чтение           |
| 27 | User                                   | custom11                     | Чтение           |
| 28 | User                                   | custom12                     | Чтение           |
| 29 | User                                   | custom13                     | Чтение           |
| 30 | User                                   | custom14                     | Чтение           |
| 31 | User                                   | empId                        | Чтение           |
| 32 | User                                   | homePhone                    | Чтение           |
| 33 | User                                   | жобфамили                    | Чтение           |
| 34 | User                                   | псевдоним                     | Чтение           |
| 35 | Пользователь                                   | state                        | Чтение           |
| 36 | User                                   | timeZone                     | Чтение           |
| 37 | User                                   | username                     | Чтение           |
| 38 | User                                   | zipCode                      | Чтение           |
| 39 | перфоне                               | ареакоде                     | Чтение           |
| 40 | перфоне                               | countryCode                  | Чтение           |
| 41 | перфоне                               | Расширение                    | Чтение           |
| 42 | перфоне                               | phoneNumber                  | Чтение           |
| 43 | перфоне                               | фонетипе                    | Чтение           |
| 44 | перемаил                               | emailAddress                 | Чтение и запись    |
| 45 | перемаил                               | emailType                    | Чтение           |
| 46 | емпемплоймент                          | фирстдатеворкед              | Чтение           |
| 47 | емпемплоймент                          | ластдатеворкед               | Чтение           |
| 48 | емпемплоймент                          | userId                       | Чтение           |
| 49 | емпемплоймент                          | исконтинжентворкер           | Чтение           |
| 50 | емпжоб                                 | каунтрйофкомпани             | Чтение           |
| 51 | емпжоб                                 | емплстатус                   | Чтение           |
| 52 | емпжоб                                 | endDate                      | Чтение           |
| 53 | емпжоб                                 | startDate                    | Чтение           |
| 54 | емпжоб                                 | jobTitle                     | Чтение           |
| 55 | емпжоб                                 | position                     | Чтение           |
| 65 | емпжоб                                 | customString13               | Чтение           |
| 56 | емпжоб                                 | managerId                    | Чтение           |
| 57 | Емпжоб \. businessunit                   | businessUnit                 | Чтение           |
| 58 | Емпжоб \. businessunit                   | бусинессунитид               | Чтение           |
| 59 | \.Компания емпжоб                        | company                      | Чтение           |
| 60 | \.Компания емпжоб                        | companyId                    | Чтение           |
| 61 | Емпжоб \. \. каунтрйофрегистратион компании | твочаркаунтрикоде           | Чтение           |
| 62 | Емпжоб \. CostCenter                     | costCenter                   | Чтение           |
| 63 | Емпжоб \. CostCenter                     | костцентерид                 | Чтение           |
| 64 | Емпжоб \. CostCenter                     | костцентердескриптион        | Чтение           |
| 65 | Емпжоб \. отдел                     | department                   | Чтение           |
| 66 | Емпжоб \. отдел                     | departmentId                 | Чтение           |
| 67 | Подразделение Емпжоб \.                       | division                     | Чтение           |
| 68 | Подразделение Емпжоб \.                       | divisionId                   | Чтение           |
| 69 | Емпжоб \. жобкоде                        | жобкоде                      | Чтение           |
| 70 | Емпжоб \. жобкоде                        | жобкодеид                    | Чтение           |
| 71 | \.Расположение емпжоб                       | локатионнаме                 | Чтение           |
| 72 | \.Расположение емпжоб                       | оффицелокатионаддресс        | Чтение           |
| 73 | \.Расположение емпжоб                       | оффицелокатионЦити           | Чтение           |
| 74 | \.Расположение емпжоб                       | officeLocationCustomString4  | Чтение           |
| 75 | \.Расположение емпжоб                       | оффицелокатионзипкоде        | Чтение           |
| 76 | Емпжоб \. пайграде                       | пайграде                     | Чтение           |
| 77 | емпемплойменттерминатион               | активимплойментскаунт       | Чтение           |
| 78 | емпемплойменттерминатион               | латесттерминатиондате        | Чтение           |

## <a name="default-attribute-mapping"></a>Сопоставление атрибутов по умолчанию

В следующей таблице представлено сопоставление атрибутов по умолчанию между атрибутами SuccessFactors, перечисленными выше, и атрибутами AD или Azure AD. В колонке "сопоставление" для приложения подготовки Azure AD можно изменить это сопоставление по умолчанию, включив в него атрибуты из списка выше. 

| \# | Сущность SuccessFactors                  | Атрибут SuccessFactors | Сопоставление атрибутов AD или Azure AD по умолчанию   | Обработка комментария                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | перперсон                              | personIdExternal         | employeeId                              | Используется как атрибут сопоставления                                                                   |
| 2  | перперсон                              | перперсонууид            | \[Не сопоставлено \- , используемое в качестве привязки к источнику\] | Во время начальной синхронизации Служба подготовки связывает Персонууид с существующим Обжектгуид\.  |
| 3  | перперсонал                            | displayName              | displayName                             | Н/Д                                                                                           |
| 4  | перперсонал                            | firstName                | givenName                               | Н/Д                                                                                           |
| 5  | перперсонал                            | lastName                 | sn                                      | Н/Д                                                                                           |
| 6  | Пользователь                                   | addressLine1             | streetAddress                           | Н/Д                                                                                           |
| 7  | Пользователь                                   | city                     | l                                       | Н/Д                                                                                           |
| 8  | Пользователь                                   | country                  | co                                      | Н/Д                                                                                           |
| 9  | Пользователь                                   | state                    | st                                      | Н/Д                                                                                           |
| 10 | User                                   | username                 | samAccountName                          | Н/Д                                                                                           |
| 11 | User                                   | zipCode                  | postalCode                              | Н/Д                                                                                           |
| 12 | перемаил                               | emailAddress             | mail                                    | Н/Д                                                                                           |
| 13 | емпжоб                                 | jobTitle                 | title                                   | Н/Д                                                                                           |
| 14 | емпжоб                                 | managerId                | manager                                 | Н/Д                                                                                           |
| 15 | Емпжоб \. \. каунтрйофрегистратион компании | твочаркаунтрикоде       | с                                       | Н/Д                                                                                           |
| 16 | Емпжоб \. отдел                     | department               | department                              | Н/Д                                                                                           |
| 17 | Подразделение Емпжоб \.                       | division                 | company                                 | Н/Д                                                                                           |
| 18 | \.Расположение емпжоб                       | оффицелокатионаддресс    | streetAddress                           | Н/Д                                                                                           |
| 19 | \.Расположение емпжоб                       | оффицелокатионзипкоде    | postalCode                              | Н/Д                                                                                           |
| 20 | емпемплойменттерминатион               | активимплойментскаунт   | AccountEnabled                          | Если Активимплойментскаунт = 0, отключите аккаунт\.                                           |
