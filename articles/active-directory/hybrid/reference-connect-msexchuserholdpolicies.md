---
title: 'Подключение Azure AD: msExchUserHoldПолитика и cloudMsExchUserHoldПолитика (ru) Документы Майкрософт'
description: В этой теме описывается поведение атрибутов атрибутов атрибутов msExchUserHoldPolicies и cloudMsExuserHoldPolicies
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c637a01825616334cda8faa594efd08f29de8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74213075"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Подключение Azure AD - msExchUserHoldПолитика и cloudMsExchUserHoldПолитика
В следующем справочном документе описаны эти атрибуты, используемые Exchange, и правильный способ отсвазания правил синхронизации по умолчанию.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Что такое msExchUserHoldPolicies и cloudMsExchUserHoldPolicies?
Для Exchange Server доступны два типа [удержаний:](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) Удержание судебного процесса и удержание места. При включении удержания судебных дел все почтовые ящики все элементы перемещаются в удержание.  Удержание In-Place используется для сохранения только тех элементов, которые отвечают критериям поискового запроса, которые вы определили с помощью инструмента In-Place eDiscovery.

Атрибуты MsExchUserHoldPolcies и cloudMsExchUserHoldPolicies позволяют на месте AD и Azure AD определять, какие пользователи находятся под удержанием в зависимости от того, используют ли они в режиме он-лайн Exchange или Exchange.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldПолитика мгновеющая сяре всбытом потоке синхронизации
По умолчанию MsExchUserHoldPolcies синхронизируется Azure AD Connect непосредственно с атрибутом msExchUserHoldPolicies в метавселенной, а затем с атрибутом msExchUserHoldPolices в Azure AD

Следующие таблицы описывают поток:

Входящий из предварительного Active Directory:

|Атрибут активного каталога|Имя атрибута|Flow type (Тип потока)|Метаверс атрибут|Правило синхронизации|
|-----|-----|-----|-----|-----|
|Локальная служба Active Directory|msExchUserHoldPolicies|Прямой доступ|msExchUserHoldPolices|В от AD - Обмен пользователей|

Выход в Azure AD:

|Метаверс атрибут|Имя атрибута|Flow type (Тип потока)|Атрибут Azure AD|Правило синхронизации|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Прямой доступ|msExchUserHoldPolicies|Выход к AAD - UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>поток синхронизации cloudMsExchUserHoldПолитикам
По умолчанию cloudMsExchUserHoldPolicies синхронизируется Azure AD Connect непосредственно с атрибутом cloudMsExchUserHoldPolicies в метавселенной. Затем, если msExchUserHoldPolices не является недействительным в метавселенной, атрибут в вытекает в Active Directory.

Следующие таблицы описывают поток:

Входящие из Azure AD:

|Атрибут активного каталога|Имя атрибута|Flow type (Тип потока)|Метаверс атрибут|Правило синхронизации|
|-----|-----|-----|-----|-----|
|Локальная служба Active Directory|cloudMsExchUserHoldПолитика|Прямой доступ|cloudMsExchUserHoldПолитика|В от AAD - Обмен пользователей|

Выход в предпосылок Active Directory:

|Метаверс атрибут|Имя атрибута|Flow type (Тип потока)|Атрибут Azure AD|Правило синхронизации|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldПолитика|ЕСЛИ (НЕ NULL)|msExchUserHoldPolicies|Выход к AD - UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Информация о поведении атрибутов
msExchangeUserHoldPolicies является единым атрибутом органа.  Один атрибут органа может быть установлен на объекте (в данном случае объекте пользователя) в каталоге на месте или в каталоге облака.  Правила запуска органа диктуют, что если атрибут синхронизирован с помещениями, то Azure AD не будет разрешено обновлять этот атрибут.

Чтобы позволить пользователям настроить политику удержания на объекте пользователя в облаке, используется атрибут cloudMSExchangeUserHoldПолитика. Этот атрибут используется потому, что Azure AD не может устанавливать msExchangeUserHoldPolicies напрямую на основе правил, изложенных выше.  Этот атрибут затем синхронизируется с каталогом на месте, если msExchangeUserHoldPolicies не является нулевым и заменяет текущую стоимость msExchangeUserHoldPolicies.

Например, при определенных обстоятельствах, если оба были изменены в помещениях и в Azure одновременно, это может вызвать некоторые проблемы.  

## <a name="next-steps"></a>Дальнейшие действия
Подробнее об [интеграции личных данных с помощью Active Directory Azure Active.](whatis-hybrid-identity.md)
