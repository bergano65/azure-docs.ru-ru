---
title: Политики хранения отчетов Azure Active Directory | Документация Майкрософт
description: Политики хранения данных отчета в Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 6188b141ec1a514d999f290366cd9ffbafc1d96c
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753138"
---
# <a name="azure-active-directory-report-retention-policies"></a>Политики хранения отчетов Azure Active Directory

Из этой статьи вы узнаете о политиках хранения данных, которые используются для разных отчетов о действиях в Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Когда Azure AD начинает сбор данных?

| Выпуск Azure AD | Начало сбора |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Когда регистрироваться для оформления подписки |
| Azure AD уровня "Бесплатный" <br /> Azure AD Basic | При первом открытии [колонки Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) или использовании [API-интерфейсов отчетности](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Когда данные о действиях становятся доступными на портале Azure?

- **Немедленно** — если вы уже работали с отчетами на портале Azure.
- **В течение 2 часов** — если вы не включили функцию создания отчетов на портале Azure.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Когда Azure AD начинает сбор данных о сигналах системы безопасности?  

Процесс сбора сигналов системы безопасности начинается, когда вы соглашаетесь использовать **центр защиты идентификации**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Как долго в Azure AD хранятся данные?

**Отчеты о действиях**    

| Отчет                 | Azure AD уровня "Бесплатный" | Azure AD Basic | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| Аудит каталога        | 7 дней        |  7 дней        | 30 дней             | 30 дней             |
| Действия при входе       | Недоступно           |  Недоступно           | 30 дней             | 30 дней             |
| Использование Azure MFA        | 30 дней       |  30 дней       | 30 дней             | 30 дней             |

Данные о действиях аудита и входа в систему можно хранить дольше указанных выше сроков хранения по умолчанию, направив их в учетную запись хранения с помощью Azure Monitor. Дополнительные сведения см. в статье [Архивация журналов Azure AD в учетной записи хранения Azure](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Сигналы системы безопасности**

| Отчет         | Azure AD уровня "Бесплатный" | Azure AD Basic | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| пользователи под угрозой;  | 7 дней        | 7 дней         | 30 дней             | 90 дней             |
| Вход, представляющий риск | 7 дней        | 7 дней         |  30 дней            | 90 дней             |

---
