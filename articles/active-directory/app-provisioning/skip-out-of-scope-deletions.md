---
title: Пропустить удаление пользователей из сферы действия (ru) Документы Майкрософт
description: Узнайте, как переопределить поведение по умолчанию, де-продавствовав из сферы действия пользователей.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1668c022a0f067a381ba09b397c7d38c99ad074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522455"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Пропустить удаление учетных записей пользователей, которые выходят за рамки

По умолчанию движок Azure AD, готовящий мягкий, удаляет или отключает пользователей, которые выходят за рамки. Однако для некоторых сценариев, таких как Workday to AD User Inbound, такое поведение может оказаться не ожидаемым, и вы можете переопределить это поведение по умолчанию.  

В этом руководстве описывается, как использовать Microsoft Graph API и исследователь Microsoft Graph API для установки флага ***SkipOutOfScopeDeletions,*** который контролирует обработку учетных записей, которые выходят за рамки. 
* Если ***SkipOutScopeDeDeLetions*** установлен на 0 (ложные), то учетные записи, которые выходят за рамки, будут отключены в целевой цели
* Если ***SkipOutScopeScopeDeletions*** установлен на 1 (правда), то учетные записи, которые выходят за рамки, не будут отключены в целевой области Этот флаг установлен на уровне *приложения подготовки* и может быть настроен с помощью API Графика. 

Поскольку эта конфигурация широко используется в приложении *workday to Active Directory,* приведенные ниже приведены приведенные ниже шаги включают скриншоты приложения Workday. Однако это также может быть использовано со **всеми другими приложениями,** такими как (ServiceNow, Salesforce, Dropbox и т.д.).

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Шаг 1: Получить ваш основной идентификатор службы предоставления приложений (Object ID)

1. Запустите [портал Azure](https://portal.azure.com)и перейдите к разделу Свойств вашего приложения подготовки. Для, например, если вы хотите экспортировать свой рабочий день в приложение *AD User Provisioning,* перейдите к разделу Свойств этого приложения. 
1. В разделе "Свойства" вашего приложения подготовки скопируйте значение GUID, связанное с полем *Идентификатор объекта*. Это значение также называется идентификатором **ServicePrincipalId** вашего приложения, и оно будет использоваться в операциях обозревателя Graph.

   ![Идентификатор субъекта-службы приложения Workday](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Шаг 2: Войти в Microsoft Graph Explorer

1. Запустите [обозреватель Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Нажмите кнопку "Вход с помощью учетной записи Майкрософт" и войдите, используя учетные данные глобального администратора Azure AD или администратора приложения.

    ![Выполнение входа в Graph](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. После выполнения входа вы увидите данные учетной записи пользователя на левой панели.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Шаг 3: Получить существующие учетные данные приложения и детали подключения

В обозревателе Microsoft Graph выполните следующий запрос GET, заменив [servicePrincipalId] на атрибут **ServicePrincipalId**, извлеченный на [шаге 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![GET запрос на работу](./media/skip-out-of-scope-deletions/skip-03.png)

Копирование ответа в текстовый файл. Он будет выглядеть как текст JSON, показанный ниже, с значениями, выделенными желтым цветом, специфичным для развертывания. Добавьте линии, выделенные зеленым цветом, до конца и обновите пароль соединения Workday, выделенный синим цветом. 

   ![GET ответ на работу](./media/skip-out-of-scope-deletions/skip-04.png)

Вот блок JSON, чтобы добавить к отображению. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Шаг 4: Обновление секретов конечной точки с флагом SkipOutScopeDeletions

В Graph Explorer запустите приведенную ниже команду для обновления конечной точки секретов с помощью флага ***SkipOutOfScopeLetions.*** 

В URL ниже заменить «servicePrincipalId» с **ServicePrincipalId** извлечены из [шага 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Скопируйте обновленный текст из шага 3 в "Орган запроса" и установите заголовок "Content-Type" на "application/json" в "Запрос заголовков". 

   ![запрос PUT](./media/skip-out-of-scope-deletions/skip-05.png)

Нажмите на кнопку "Запуск запроса". 

Вы должны получить выход как "Успех - Статус код 204". 

   ![PUT ответ](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Шаг 5: Проверить, что из сферы пользователи не получают отключены

Вы можете проверить результаты этого флага в ожидаемом поведении, обновив правила сканирования, чтобы пропустить конкретного пользователя. В приведенном ниже примере мы исключаем сотрудника с ID 21173 (который ранее был в области применения), добавляя новое правило скопирования: 

   ![Пример скопирования](./media/skip-out-of-scope-deletions/skip-07.png)

В следующем цикле подготовки служба подготовки Azure AD определит, что пользователь 21173 вышел из сферы действия, и если включено свойство SkipOutOfScopeLetletions, то правило синхронизации для этого пользователя будет отображать сообщение, как показано ниже: 

   ![Пример скопирования](./media/skip-out-of-scope-deletions/skip-08.png)


