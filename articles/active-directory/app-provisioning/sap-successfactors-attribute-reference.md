---
title: SAP SuccessFactors Справка по attribute (ru) Документы Майкрософт
description: Узнайте, какие атрибуты от SuccessFactors поддерживаются подготовкой SuccessFactors-HR
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522362"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP SuccessFactors Attribute Справка

## <a name="supported-successfactors-entities-and-attributes"></a>Поддерживаемые организации и атрибуты SuccessFactors

В таблице ниже отражен список атрибутов SuccessFactors, поддерживаемых двумя приложениями для подготовки: 
* [SuccessFactors to Active Directory User Provisioning](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors to Azure AD User Provisioning](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Успешноесущность                  | SuccessFactors Attribute     | Тип операции |
|----|----------------------------------------|------------------------------|----------------|
| 1  | Перперсон                              | personIdExternal             | Чтение           |
| 2  | Перперсон                              | personId                     | Чтение           |
| 3  | Перперсон                              | perPersonUuid                | Чтение           |
| 4  | PerPersonal                            | displayName                  | Чтение           |
| 5  | PerPersonal                            | firstName                    | Чтение           |
| 6  | PerPersonal                            | gender                       | Чтение           |
| 7  | PerPersonal                            | lastName                     | Чтение           |
| 8  | PerPersonal                            | middleName                   | Чтение           |
| 9  | PerPersonal                            | предпочтительноеИмя                | Чтение           |
| 10 | Пользователь                                   | addressLine1                 | Чтение           |
| 11 | Пользователь                                   | addressLine2                 | Чтение           |
| 12 | Пользователь                                   | addressLIne3                 | Чтение           |
| 13 | Пользователь                                   | businessPhone                | Чтение           |
| 14 | Пользователь                                   | Телефон                    | Чтение           |
| 15 | Пользователь                                   | city                         | Чтение           |
| 16 | Пользователь                                   | country                      | Чтение           |
| 17 | Пользователь                                   | custom01                     | Чтение           |
| 18 | Пользователь                                   | custom02                     | Чтение           |
| 19 | Пользователь                                   | custom03                     | Чтение           |
| 20 | Пользователь                                   | custom04                     | Чтение           |
| 21 | Пользователь                                   | custom05                     | Чтение           |
| 22 | Пользователь                                   | custom06                     | Чтение           |
| 23 | Пользователь                                   | custom07                     | Чтение           |
| 24 | Пользователь                                   | custom08                     | Чтение           |
| 25 | Пользователь                                   | custom09                     | Чтение           |
| 26 | Пользователь                                   | custom10                     | Чтение           |
| 27 | Пользователь                                   | custom11                     | Чтение           |
| 28 | Пользователь                                   | custom12                     | Чтение           |
| 29 | Пользователь                                   | custom13                     | Чтение           |
| 30 | Пользователь                                   | custom14                     | Чтение           |
| 31 | Пользователь                                   | empId                        | Чтение           |
| 32 | Пользователь                                   | homePhone                    | Чтение           |
| 33 | Пользователь                                   | работаСемья                    | Чтение           |
| 34 | Пользователь                                   | псевдоним                     | Чтение           |
| 35 | Пользователь                                   | state                        | Чтение           |
| 36 | Пользователь                                   | timeZone                     | Чтение           |
| 37 | Пользователь                                   | username                     | Чтение           |
| 38 | Пользователь                                   | Индекс                      | Чтение           |
| 39 | Перфон                               | AreaCode                     | Чтение           |
| 40 | Перфон                               | countryCode                  | Чтение           |
| 41 | Перфон                               | Расширение                    | Чтение           |
| 42 | Перфон                               | phoneNumber                  | Чтение           |
| 43 | Перфон                               | phoneType                    | Чтение           |
| 44 | PerEmail                               | emailAddress                 | Чтение и запись    |
| 45 | PerEmail                               | emailType                    | Чтение           |
| 46 | EmpEmployment                          | firstDateWorked              | Чтение           |
| 47 | EmpEmployment                          | lastDateWorked               | Чтение           |
| 48 | EmpEmployment                          | userId                       | Чтение           |
| 49 | EmpEmployment                          | isContingentWorker           | Чтение           |
| 50 | Эмпджоб                                 | countryOfCompany             | Чтение           |
| 51 | Эмпджоб                                 | emplStatus                   | Чтение           |
| 52 | Эмпджоб                                 | endDate                      | Чтение           |
| 53 | Эмпджоб                                 | startDate                    | Чтение           |
| 54 | Эмпджоб                                 | jobTitle                     | Чтение           |
| 55 | Эмпджоб                                 | position                     | Чтение           |
| 65 | Эмпджоб                                 | customString13               | Чтение           |
| 56 | Эмпджоб                                 | managerId                    | Чтение           |
| 57 | Бизнес-единица EmpJob\.                   | businessUnit                 | Чтение           |
| 58 | Бизнес-единица EmpJob\.                   | businessUnitId               | Чтение           |
| 59 | Компания EmpJob\.                        | company                      | Чтение           |
| 60 | Компания EmpJob\.                        | companyId                    | Чтение           |
| 61 | EmpJob\.\.Компания СтранаРегистрация | дваЧарСтранаКод           | Чтение           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Чтение           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Чтение           |
| 64 | EmpJob\.CostCenter                     | costCenterОписание        | Чтение           |
| 65 | Департамент EmpJob\.                     | department                   | Чтение           |
| 66 | Департамент EmpJob\.                     | departmentId                 | Чтение           |
| 67 | Подразделение Эмпджоум\.                       | division                     | Чтение           |
| 68 | Подразделение Эмпджоум\.                       | divisionId                   | Чтение           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Чтение           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Чтение           |
| 71 | Расположение Эмпджода\.                       | МестоположениеИмя                 | Чтение           |
| 72 | Расположение Эмпджода\.                       | officeLocationАдрес        | Чтение           |
| 73 | Расположение Эмпджода\.                       | officeLocationCity           | Чтение           |
| 74 | Расположение Эмпджода\.                       | officeLocationCustomString4  | Чтение           |
| 75 | Расположение Эмпджода\.                       | officeLocationIpCode        | Чтение           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Чтение           |
| 77 | EmpEmploymentTermination               | активныетрудовыекСчет       | Чтение           |
| 78 | EmpEmploymentTermination               | последниеТерминдата        | Чтение           |


## <a name="default-attribute-mapping"></a>Отображение атрибутов по умолчанию

В приведенной ниже таблице представлено отображение атрибутов по умолчанию между атрибутами SuccessFactors, перечисленными выше, и атрибутами AD/Azure AD. В приложении Azure AD для подготовки "Картирование" можно изменить это отображение по умолчанию, чтобы включить атрибуты из списка выше. 

| \# | Успешноесущность                  | SuccessFactors Attribute | Отображение атрибутов AD/Azure AD   | Обработка Ремарка                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | Перперсон                              | personIdExternal         | employeeId                              | Используется в качестве атрибута соответствия                                                                   |
| 2  | Перперсон                              | perPersonUuid            | \[Не отображено, \- используемое в качестве якоря источника\] | Во время первоначальной синхронизации Служба обеспечения связывает человека с существующим objectGuid.  |
| 3  | PerPersonal                            | displayName              | displayName                             | Н/Д                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | Н/Д                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | Н/Д                                                                                           |
| 6  | Пользователь                                   | addressLine1             | streetAddress                           | Н/Д                                                                                           |
| 7  | Пользователь                                   | city                     | l                                       | Н/Д                                                                                           |
| 8  | Пользователь                                   | country                  | co                                      | Н/Д                                                                                           |
| 9  | Пользователь                                   | state                    | st                                      | Н/Д                                                                                           |
| 10 | Пользователь                                   | username                 | samAccountName                          | Н/Д                                                                                           |
| 11 | Пользователь                                   | Индекс                  | postalCode                              | Н/Д                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | Н/Д                                                                                           |
| 13 | Эмпджоб                                 | jobTitle                 | title                                   | Н/Д                                                                                           |
| 14 | Эмпджоб                                 | managerId                | manager                                 | Н/Д                                                                                           |
| 15 | EmpJob\.\.Компания СтранаРегистрация | дваЧарСтранаКод       | с                                       | Н/Д                                                                                           |
| 16 | Департамент EmpJob\.                     | department               | department                              | Н/Д                                                                                           |
| 17 | Подразделение Эмпджоум\.                       | division                 | company                                 | Н/Д                                                                                           |
| 18 | Расположение Эмпджода\.                       | officeLocationАдрес    | streetAddress                           | Н/Д                                                                                           |
| 19 | Расположение Эмпджода\.                       | officeLocationIpCode    | postalCode                              | Н/Д                                                                                           |
| 20 | EmpEmploymentTermination               | активныетрудовыекСчет   | AccountEnabled                          | если activeEmploymentsCount'0, отключить учетную запись.                                           |

