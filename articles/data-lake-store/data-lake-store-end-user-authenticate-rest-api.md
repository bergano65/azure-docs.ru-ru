---
title: 'Аутентификация пользователей: REST API с Gen1 хранилища Озера данных Azure с помощью Azure Active Directory | Документация Майкрософт'
description: Узнайте, как реализовать проверку подлинности пользователей в Azure Data Lake Storage 1-го поколения с помощью Azure Active Directory и REST API.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 0ef65c23ee1bf4f064695779b71c8616427da204
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881449"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Проверка подлинности пользователей в Azure Data Lake Storage 1-го поколения с помощью REST API
> [!div class="op_single_selector"]
> * [Использование Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Использование пакета .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Использование Python](data-lake-store-end-user-authenticate-python.md)
> * [Использование интерфейса REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

В этой статье описывается, как использовать REST API для проверки подлинности пользователей с помощью Azure Data Lake Storage 1-го поколения. См. дополнительные сведения о [проверке подлинности между службами в Data Lake Storage 1-го поколения с помощью REST API](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Технические условия

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Создание собственного приложения Active Directory** Вам нужно выполнить инструкции по [аутентификации пользователей в Data Lake Storage 1-го поколения с помощью Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **[cURL](https://curl.haxx.se/)**. В этой статье для демонстрации вызовов REST API к учетной записи Data Lake Storage 1-го поколения используется cURL.

## <a name="end-user-authentication"></a>Аутентификация пользователей
Мы рекомендуем использовать аутентификацию пользователей, если вам нужно, чтобы пользователи входили в приложение с помощью Azure AD. Приложение будет иметь тот же уровень доступа к ресурсам Azure, что и вошедший пользователь. Пользователю нужно периодически вводить свои учетные данные, чтобы обеспечить для приложения возможность доступа.

В результате входа пользователя приложение получает маркер доступа и маркер обновления. Маркер доступа вкладывается в каждый запрос к Data Lake Storage 1-го поколения и Data Lake Analytics и по умолчанию действителен в течение одного часа. Маркер обновления может использоваться для получения нового маркера доступа. По умолчанию он действителен до двух недель, при условии, что используется регулярно. Для входа пользователей можно использовать два разных подхода.

В этом сценарии приложение предлагает пользователю войти в систему, и все операции выполняются в контексте пользователя. Выполните следующие действия:

1. В приложении перенаправьте пользователя на следующий URL-адрес.

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

   > [!NOTE]
   > \<<REDIRECT-URI> должен быть закодирован для использования в URL-адресе. В качестве https://localhost используйте `https%3A%2F%2Flocalhost`.

    В целях обучения можно заменить значения заполнителей в URL-адресе выше и вставить его в адресную строку веб-браузера. Вы перейдете на страницу аутентификации с помощью учетной записи Azure. Войдя в систему, вы увидите ответ в адресной строке браузера. Ответ имеет следующий формат.

        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Запишите код авторизации из ответа. В этом руководстве вы можете скопировать код авторизации из адресной строки веб-браузера и передать его в запрос POST к конечной точке маркера, как показано в следующем фрагменте кода:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<APPLICATION-ID> \
        -F code=<AUTHORIZATION-CODE>

   > [!NOTE]
   > В этом случае кодировать \<REDIRECT-URI> не нужно.
   > 
   > 

3. Ответ является объектом JSON, который содержит маркер доступа (например, `"access_token": "<ACCESS_TOKEN>"`) и маркер обновления (например, `"refresh_token": "<REFRESH_TOKEN>"`). Приложение использует маркер доступа для обращения к Azure Data Lake Storage 1-го поколения, а маркер обновления — для получения другого маркера доступа, когда срок действия текущего маркера доступа истечет.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Когда срок действия маркера доступа истечет, вы можете запросить новый маркер доступа, используя маркер обновления, как показано в следующем фрагменте кода:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Дополнительные сведения об интерактивной проверке подлинности пользователей см. в статье [Авторизация доступа к веб-приложениям с помощью OAuth 2.0 и Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645542.aspx).

## <a name="next-steps"></a>Дальнейшие действия
В этой статье описывается, как использовать проверку подлинности между службами, чтобы реализовать проверку подлинности в Azure Data Lake Storage 1-го поколения с помощью REST API. Дополнительные сведения об использовании REST API для работы с Azure Data Lake Storage 1-го поколения см. в следующих статьях.

* [Начало работы с Gen1 хранилища Озера данных с помощью REST API](data-lake-store-get-started-rest-api.md)
* [Операции с данными в поколение 1 хранилища Озера данных с помощью REST API](data-lake-store-data-operations-rest-api.md)

