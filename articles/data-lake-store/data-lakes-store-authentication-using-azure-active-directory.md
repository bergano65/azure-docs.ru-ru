---
title: Аутентификация в ADLS 1-го поколения с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как выполнять аутентификацию в ADLS 1-го поколения с помощью Azure Active Directory.
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
ms.openlocfilehash: f83cf183bee930dd07c707b0eb49125cecd70b84
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884641"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Аутентификация в ADLS 1-го поколения с помощью Azure Active Directory

Azure Data Lake Storage 1-го поколения (ADLS 1-го поколения) использует Azure Active Directory для аутентификации. Прежде чем создать приложение, которое работает с ADLS 1-го поколения, необходимо решить, как для него выполнять аутентификацию в Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Параметры проверки подлинности

* **Аутентификация пользователей**. Для аутентификации в ADLS 1-го поколения используются учетные данные пользователя Azure. Приложение, создаваемое для работы с ADLS 1-го поколения, запрашивает эти учетные данные пользователя. В результате такой механизм аутентификации является *интерактивным*, и приложение запускается в контексте пользователя, вошедшего в систему. Дополнительные сведения и инструкции приведены в статье [Аутентификация пользователей в Data Lake Store с помощью Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Аутентификация между службами**. Используйте этот вариант, если требуется, чтобы приложение проходило аутентификацию в ADLS 1-го поколения. В таких случаях можно создать приложение Azure Active Directory (AD) и использовать ключ из приложения Azure AD для аутентификации в ADLS 1-го поколения. Такой механизм аутентификации будет *неинтерактивным*. Дополнительные сведения и инструкции приведены в статье [Аутентификация между службами в Data Lake Store с помощью Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

В следующей таблице показано, как механизмы аутентификации пользователей и служб поддерживаются для ADLS 1-го поколения. Ниже приведены инструкции к этой таблице:

* символ ✔* означает, что этот вариант аутентификации поддерживается, и можно перейти по ссылке на статью, в которой показано, как его использовать; 
* символ ✔ означает, что этот вариант аутентификации поддерживается; 
* пустые ячейки означают, что этот вариант аутентификации не поддерживается.


|Использование варианта аутентификации для…                   |.NET         |Java     |PowerShell |Инфраструктура CLI Azure | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Аутентификация пользователей (без MFA**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(не рекомендуется)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Аутентификация пользователей (с MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Аутентификация между службами (с помощью ключа клиента)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Аутентификация между службами (с помощью сертификата клиента) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Щелкните <b>✔\* </b> символов. Это ссылка.</i><br>
<i>** MFA — это многофакторная проверка подлинности</i>

Дополнительные сведения об использовании Azure Active Directory для аутентификации см. в статье [Сценарии аутентификации в Azure Active Directory](../active-directory/develop/authentication-scenarios.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Аутентификация пользователей](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Взаимодействие между службами](data-lake-store-service-to-service-authenticate-using-active-directory.md)


