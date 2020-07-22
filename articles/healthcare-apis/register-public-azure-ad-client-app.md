---
title: Регистрация общедоступного клиентского приложения в Azure AD с помощью API Azure для FHIR
description: В этой статье объясняется, как зарегистрировать общедоступное клиентское приложение в Azure Active Directory для подготовки к развертыванию API FHIR в Azure.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 5aa9e5a33dbe66e3ebd787decfa3a520454fc6f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871004"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Регистрация общедоступного клиентского приложения в Azure Active Directory

В этой статье вы узнаете, как зарегистрировать общедоступное приложение в Azure Active Directory.  

Регистрация клиентских приложений — это Azure Active Directory представления приложений, которые могут проходить проверку подлинности и запрашивать разрешения API от имени пользователя. Общедоступные клиенты — это приложения, такие как мобильные приложения и одностраничные приложения JavaScript, которые не могут оставаться конфиденциальными. Процедура аналогична [регистрации конфиденциального клиента](register-confidential-azure-ad-client-app.md), но, поскольку общедоступные клиенты не могут быть доверенными для хранения секрета приложения, добавлять его не нужно.

## <a name="app-registrations-in-azure-portal"></a>Регистрация приложений в портал Azure

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Azure Active Directory**.

2. В колонке **Azure Active Directory** щелкните **Регистрация приложений**:

    ![портал Azure. Регистрация нового приложения.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Щелкните **новую регистрацию**.

## <a name="application-registration-overview"></a>Обзор регистрации приложения

1. Присвойте приложению отображаемое имя.

2. Укажите URL-адрес ответа. URL-адрес ответа — это место, где коды проверки подлинности будут возвращены клиентскому приложению. Позднее можно добавить URL-адреса ответов и изменить их.

    ![портал Azure. Новая регистрация общедоступного приложения.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>Разрешения API

Аналогично [клиентскому приложению для конфиденциальных](register-confidential-azure-ad-client-app.md)данных необходимо выбрать разрешения API, которые приложение должно запрашивать от имени пользователей:

1. Откройте **разрешения API**.

    Если вы используете API Azure для FHIR, вы добавите разрешение для API здравоохранения Azure, выполнив поиск по API-интерфейсам Azure для здравоохранения в разделе API, которые **использует Моя организация** (см. рисунок ниже).
    
    Если вы ссылаетесь на другое приложение ресурсов, выберите [регистрацию приложения API FHIR](register-resource-azure-ad-client-app.md) , созданную ранее в разделе **Мои API**:

    ![портал Azure. Новые разрешения общедоступного API — API Azure для FHIR по умолчанию](media/public-client-app/api-permissions.png)


2. Выберите разрешения, которые вы хотите предоставить приложению для запроса: ![ портал Azure. Разрешения приложений](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>Проверка центра сервера FHIR
Если приложение, зарегистрированное в этой статье и сервере FHIR, находится в одном клиенте Azure AD, то можно перейти к дальнейшим действиям.

Если клиентское приложение настроено в другом клиенте Azure AD с сервера FHIR, необходимо будет обновить **центр**. В API Azure для FHIR вы устанавливаете центр в разделе "Параметры"--> проверки подлинности. Задайте для параметра центр сертификации значение **https://login.microsoftonline.com/\<TENANT-ID>** .

## <a name="next-steps"></a>Дальнейшие шаги

В этой статье вы узнали, как зарегистрировать общедоступное клиентское приложение в Azure Active Directory. Затем проверьте доступ к серверу FHIR с помощью процедуры POST.
 
>[!div class="nextstepaction"]
>[Получение доступа к Azure API для FHIR с помощью Postman](access-fhir-postman-tutorial.md)
