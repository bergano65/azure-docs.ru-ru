---
title: Часто задаваемые вопросы условного доступа Azure Active Directory | Документация Майкрософт
description: Получите ответы на часто задаваемые вопросы об условном доступе в Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.subservice: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 111daef3448533cd863d45197d45c87d8d21264b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112396"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Часто задаваемые вопросы условного доступа Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>Какие приложения работают с политиками условного доступа?

Сведения о приложениях, которые работают с политиками условного доступа, см. в разделе [приложения и браузеры, использующие правила условного доступа в Azure Active Directory](technical-reference.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Политики условного доступа применяются для совместной работы B2B и гостевых пользователей?

Политики применяются для пользователей службы совместной работы B2B. Однако в некоторых случаях пользователь может не иметь возможности соответствовать требованиям политики. Например, организация гостевого пользователя может не поддерживать многофакторную проверку подлинности. 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Распространяется ли политика SharePoint Online на OneDrive для бизнеса?

Да. Политика SharePoint Online также распространяется на OneDrive для бизнеса.


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Почему не удается задать политику для клиентских приложений, таких как Word или Outlook?

Политики условного доступа устанавливает требования для доступа к службе. Она применяется, когда происходит проверка подлинности для этой службы. Политика не устанавливается напрямую в клиентском приложении. Вместо этого она применяется, когда клиент вызывает службу. Например, политика, заданная в SharePoint, применяется к клиентам, вызывающим SharePoint. Политика, заданная в Exchange, применяется к Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Распространяется ли политика условного доступа для учетных записей служб?

Политики условного доступа применяются ко всем учетным записям пользователей. Это относится и к учетным записям пользователей, которые используются как учетные записи служб. Часто учетную запись службы, которая выполняется автоматически, не может удовлетворить требованиям политики условного доступа. Например, может потребоваться многофакторная проверка подлинности. Учетные записи служб можно исключить из политики с помощью параметров управления политикой условного доступа. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Доступны ли API-интерфейсов Graph для настройки политики условного доступа?

В настоящее время нет. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Какова политика исключений по умолчанию для не поддерживаемых платформ?

В настоящее время политики условного доступа применяются избирательно к на пользователей устройств iOS и Android. На других платформах по умолчанию не влияет на приложения с помощью политики условного доступа для устройств iOS и Android. Администратор клиента может переопределить глобальную политику, чтобы запретить доступ пользователям на платформах, которые не поддерживаются.


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Как работают политики условного доступа для Microsoft Teams?

Microsoft Teams в значительной степени зависит от Exchange Online и SharePoint Online в основных сценариях производительности, таких как собрания, календари и совместное использование файлов. Политики условного доступа, заданные для этих облачных приложений применяются к Microsoft Teams при входе пользователя непосредственно в Microsoft Teams.

Microsoft Teams также поддерживается отдельно как облачного приложения в политиках Azure Active Directory условного доступа. Политики условного доступа, заданные для облачного приложения применяются к Microsoft Teams при входе пользователя. Однако без правильных политик для других приложений, таких как Exchange Online и SharePoint Online, пользователям по-прежнему может предоставляться к ним прямой доступ.

Клиенты рабочего стола Microsoft Teams для Windows и Mac поддерживают современные методы проверки подлинности. Современные методы проверки подлинности используют вход на основе библиотеки проверки подлинности Active Directory (ADAL) для клиентских приложений Microsoft Office на всех платформах.
