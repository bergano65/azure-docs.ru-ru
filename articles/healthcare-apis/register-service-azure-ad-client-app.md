---
title: Регистрация приложения службы в Azure AD с помощью API Azure для FHIR
description: Узнайте, как зарегистрировать клиентское приложение службы в Azure Active Directory, которое можно использовать для проверки подлинности и получения маркеров.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 34eec3ad0d2fc193744898b6f08cbe50c261c945
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853030"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Регистрация клиентского приложения службы в Azure Active Directory

В этой статье вы узнаете, как зарегистрировать клиентское приложение службы в Azure Active Directory. Регистрации клиентских приложений — это Azure Active Directory представления приложений, которые можно использовать для проверки подлинности и получения маркеров. Клиент службы предназначен для использования приложением для получения маркера доступа без интерактивной проверки подлинности пользователя. Он будет иметь определенные разрешения приложения и использовать секрет приложения (пароль) при получении маркеров доступа.

Чтобы создать новый клиент службы, выполните следующие действия.

## <a name="app-registrations-in-azure-portal"></a>Регистрация приложений в портал Azure

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Azure Active Directory**.

2. В колонке **Azure Active Directory** щелкните **Регистрация приложений**:

    ![портал Azure. Регистрация нового приложения.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Щелкните **Новая регистрация**.

## <a name="service-client-application-details"></a>Сведения о клиентском приложении службы

* Клиенту службы требуется отображаемое имя. Кроме того, можно указать URL-адрес ответа, но обычно он не используется.

    ![портал Azure. Новая регистрация клиентского приложения службы.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>Разрешения API

Вам потребуется предоставить роли клиентского приложения службы. 

1. Откройте **разрешения API** и выберите [регистрацию приложения API FHIR](register-resource-azure-ad-client-app.md). Если вы используете API Azure для FHIR, вы добавите разрешение для API здравоохранения Azure, выполнив поиск по API-интерфейсам Azure для здравоохранения в разделе API, которые **использует Моя организация**.

    ![портал Azure. Разрешения API клиента службы](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Выберите роли приложения из тех, которые определены в приложении ресурсов:

    ![портал Azure. Разрешения клиентского приложения службы](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Предоставьте согласие для приложения. Если у вас нет необходимых разрешений, обратитесь к администратору Azure Active Directory.

    ![портал Azure. Согласие администратора клиента службы](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Секрет приложения

Клиенту службы требуется секрет (пароль), который будет использоваться при получении маркеров.

1. Щелкните ** &amp; секреты сертификаты** .

2. Щелкните **Создать секрет клиента**.

    ![портал Azure. Секрет клиента службы](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Укажите длительность секрета.

4. После создания он будет отображаться на портале только один раз. Запишите его и храните в безопасном месте.

## <a name="next-steps"></a>Дальнейшие шаги

В этой статье вы узнали, как зарегистрировать клиентское приложение службы в Azure Active Directory. Затем разверните API FHIR в Azure.
 
>[!div class="nextstepaction"]
>[Развертывание сервера FHIR с открытым исходным кодом](fhir-oss-powershell-quickstart.md)