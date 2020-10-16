---
title: Тестирование безопасности Azure v2 — защита данных
description: Защита данных с учетом производительности Azure с производительностью 2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ab42acbc07072f48d28b3adb5bc4c65672f257bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314323"
---
# <a name="security-control-v2-data-protection"></a>Управление безопасностью v2: защита данных

Защита данных охватывает управление защитой данных при хранении, передачей и через полномочные механизмы доступа. Сюда входит обнаружение, классификация, защита и мониторинг ресурсов конфиденциальных данных с помощью контроля доступа, шифрования и ведения журнала в Azure.

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: обнаружение, классификация и метка конфиденциальных данных

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| DP-1 | 13,1, 14,5, 14,7 | SC-28 |

Выявлять, классифицировать и помечать конфиденциальные данные, чтобы можно было разрабатывать соответствующие элементы управления для обеспечения хранения, обработки и передачи конфиденциальных данных в технологические системы Организации. 

Используйте Azure Information Protection (и связанное с ним средство проверки) для конфиденциальной информации в документах Office в Azure, в локальной среде, в Office 365 и в других местах. 

Вы можете использовать Information Protection Azure SQL для помощи в классификации и маркировке информации, хранящейся в базах данных SQL Azure.

- [Использование тегов для конфиденциальной информации с помощью Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Как реализовать обнаружение данных SQL Azure](/azure/sql-database/sql-database-data-discovery-and-classification)

**Ответственность**: Совмещаемая блокировка

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Безопасность приложений и DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)  

- [Безопасность данных](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: Защита конфиденциальных данных

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| DP-2 | 13,2, 2,10 | SC-7, AC-4 |

Защитите конфиденциальные данные, запретив доступ с помощью управления доступом на основе ролей Azure (Azure RBAC), управления доступом на основе сети и определенных элементов управления в службах Azure (например, шифрование в SQL и других базах данных). 

Чтобы обеспечить постоянный контроль доступа, все типы управления доступом должны быть согласованы с вашей корпоративной стратегией сегментации. Стратегия сегментации предприятия также должна быть уведомлена о расположении конфиденциальных или важных бизнес-данных и систем.

Для базовой платформы, управляемой корпорацией Майкрософт, корпорация Майкрософт считает все содержимое клиента конфиденциальным и защищенным от потери и доступности данных клиента. Чтобы обеспечить безопасность данных клиентов в Azure, корпорация Майкрософт реализовала Некоторые элементы управления и возможности по обеспечению защиты данных по умолчанию.

- [Управление доступом на основе ролей (RBAC) в Azure](../../role-based-access-control/overview.md)

- [Общие сведения о защите данных клиентов в Azure](../fundamentals/protection-customer-data.md)

**Ответственность**: Совмещаемая блокировка

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Безопасность приложений и DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Безопасность данных](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: отслеживание несанкционированных передач конфиденциальных данных

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| DP-3 | 13,3 | AC-4, SI-4 |

Наблюдение за несанкционированным переносом данных в расположения за пределами корпоративной видимости и контроля. Обычно это подразумевает наблюдение за аномальными действиями (большими или необычными передачами), которые могут означать несанкционированный утечка данных. 

Расширенная защита от угроз в службе хранилища Azure (ATP) и Azure SQL ATP могут оповещать об аномальных передачах информации, которая может указывать на несанкционированную передачу конфиденциальной информации. 

Azure Information Protection (АДМИНИСТРАТИВная защита) предоставляет возможности мониторинга для сведений, которые были классифицированы и помечены. 

Если это необходимо для обеспечения соответствия защиты от потери данных (DLP), можно использовать решение DLP на основе узла, чтобы принудительно применять выявляющий и/или превентивные элементы управления для предотвращения утечка данных.

- [Включение Azure SQL ATP](../../azure-sql/database/threat-detection-overview.md)

- [Включение ATP в службе хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Ответственность**: Совмещаемая блокировка

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Операции безопасности](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Безопасность приложений и DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: шифрование конфиденциальной информации во время передачи

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| DP-4 | 14,4 | SC-8 |

Для дополнения элементов управления доступом данные в передаче должны быть защищены от атак с использованием аппаратного контроллера управления (например, для записи трафика) с помощью шифрования, чтобы злоумышленники не могли легко считывать или изменять данные. 

Хотя это необязательно для трафика в частных сетях, это важно для трафика во внешних и общедоступных сетях. Для трафика HTTP убедитесь, что все клиенты, подключающиеся к ресурсам Azure, могут согласовать TLS v 1.2 или более поздней версии. Для удаленного управления используйте SSH (для Linux) или RDP/TLS (для Windows) вместо незашифрованного протокола. Устаревшие версии и протоколы SSL, TLS и SSH, а слабые шифры должны быть отключены.  

По умолчанию Azure обеспечивает шифрование данных при передаче между центрами обработки данных Azure. 

- [Общие сведения о шифровании при передаче с помощью Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Сведения о безопасности TLS](/security/engineering/solving-tls1-problem)

- [Двойное шифрование для данных Azure при передаче](../fundamentals/double-encryption.md#data-in-transit)

**Ответственность**: Совмещаемая блокировка

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Архитектура безопасности](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Безопасность приложений и DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Безопасность данных](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Шифрование конфиденциальных неактивных данных

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| DP-5 | 14,8 | SC-28, SC-12 |

Для дополнения элементов управления доступом неактивных данных следует защищать от атак с использованием аппаратного контроллера управления (например, доступ к базовому хранилищу) с помощью шифрования. Это гарантирует, что злоумышленники не смогут легко считывать или изменять данные. 

По умолчанию Azure обеспечивает шифрование неактивных данных. Для высокочувствительных данных вы можете реализовать дополнительные возможности шифрования при хранении во всех ресурсах Azure, где это возможно. Azure управляет ключами шифрования по умолчанию, но Azure предоставляет возможности для управления собственными ключами (управляемыми клиентом ключами) для определенных служб Azure.

- [Общие сведения о шифровании неактивных в Azure](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Настройка ключей шифрования, управляемых клиентом](../../storage/common/storage-encryption-keys-portal.md)

- [Модель шифрования и таблица управления ключами](../fundamentals/encryption-models.md)

- [Двойное шифрование неактивных данных в Azure](../fundamentals/double-encryption.md#data-at-rest)

**Ответственность**: Совмещаемая блокировка

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Архитектура безопасности](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Безопасность приложений и DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Безопасность данных](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

