---
title: Регистрация конфиденциального клиентского приложения в Azure AD — API Azure для FHIR
description: Зарегистрируйте конфиденциальное клиентское приложение в Azure Active Directory, которое проходит проверку подлинности от имени пользователя и запрашивает доступ к приложениям ресурсов.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 756645d2df22f1222c3004a44e5a46c7a3bc1a2f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852554"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Регистрация конфиденциального клиентского приложения в Azure Active Directory

В этом руководстве вы узнаете, как зарегистрировать конфиденциальное клиентское приложение в Azure Active Directory. 

Регистрация клиентского приложения — это Azure Active Directory представление приложения, которое можно использовать для проверки подлинности от имени пользователя и запроса доступа к [приложениям ресурсов](register-resource-azure-ad-client-app.md). Конфиденциальное клиентское приложение — это приложение, которое может быть доверенным для хранения секрета и представлять этот секрет при запросе маркеров доступа. Примерами конфиденциальных приложений являются приложения на стороне сервера.

Чтобы зарегистрировать новое конфиденциальное приложение на портале, выполните следующие действия.

## <a name="app-registrations-in-azure-portal"></a>Регистрация приложений в портал Azure

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Azure Active Directory**.

2. В колонке **Azure Active Directory** щелкните **Регистрация приложений**:

    ![портал Azure. Регистрация нового приложения.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Щелкните **новую регистрацию**.

## <a name="register-a-new-application"></a>Регистрация нового приложения

1. Присвойте приложению отображаемое имя.

2. Укажите URL-адрес ответа. Эти сведения можно изменить позже, но если вы знакомы с URL-адресом ответа приложения, введите его сейчас.

    ![Регистрация нового конфиденциального клиентского приложения.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>Разрешения API

Далее добавьте разрешения API:

1. Откройте **разрешения API**:

    ![Конфиденциальный клиент. Разрешения API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Нажмите кнопку **Добавить разрешение** .

3. Выберите соответствующий API ресурсов:

    Для API Azure для FHIR (Управляемая служба) щелкните **интерфейсы API, используемые моей организацией** , и выполните поиск по запросу "API-интерфейсы Azure для здравоохранения". Для сервера FHIR с открытым исходным кодом для Azure выберите свой [ресурс API FHIR регистрация приложения](register-resource-azure-ad-client-app.md):

    ![Конфиденциальный клиент. Мои API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Выберите области (разрешения), которые должны иметь конфиденциальное приложение для запроса от имени пользователя:

    ![Конфиденциальный клиент. Делегированные разрешения](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Секрет приложения

1. Создайте секрет приложения (секрет клиента):

    ![Конфиденциальный клиент. Секрет приложения](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Укажите описание и длительность секрета.

3. После создания он будет отображаться на портале только один раз. Запишите его и храните в безопасном месте.

## <a name="next-steps"></a>Дальнейшие шаги

В этой статье вы узнали, как зарегистрировать конфиденциальное клиентское приложение в Azure Active Directory. Теперь вы готовы к развертыванию [API Azure для FHIR](fhir-paas-powershell-quickstart.md).

После развертывания API Azure для FHIR можно ознакомиться с дополнительными доступными параметрами.
 
>[!div class="nextstepaction"]
>[Развертывание Azure API для FHIR](fhir-paas-powershell-quickstart.md)