---
title: 'Проверки подлинности в службе: Поколение 1 хранилища Озера данных Azure с Azure Active Directory | Документация Майкрософт'
description: Узнайте, как реализовать аутентификацию между службами в Azure Data Lake Storage 1-го поколения с помощью Azure Active Directory.
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
ms.openlocfilehash: a7fdcf396f586a65efa17e489d002f1c8847a193
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58884998"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Аутентификация между службами в Azure Data Lake Storage 1-го поколения с помощью Azure Active Directory
> [!div class="op_single_selector"]
> * [Аутентификация пользователей](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Аутентификация между службами](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage 1-го поколения (ADLS 1-го поколения) использует Azure Active Directory для аутентификации. Прежде чем создать приложение, которое работает с ADLS 1-го поколения, необходимо решить, как для него выполнять аутентификацию в Azure Active Directory (Azure AD). Доступно два основных варианта:

* Аутентификация пользователей 
* Аутентификация между службами (в этой статье) 

Оба варианта позволят приложению получить маркер OAuth 2.0, который используется в каждом запросе к Data Lake Storage 1-го поколения.

Из этой статьи вы узнаете, как создать **веб-приложение Azure AD для аутентификации между службами**. Инструкции по настройке приложения Azure AD для аутентификации пользователей см. в статье [Аутентификация пользователей в Data Lake Storage 1-го поколения с помощью Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Технические условия
* Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Шаг 1. Создание веб-приложения Active Directory

Создайте и настройте веб-приложение Azure AD для аутентификации между службами в Azure Data Lake Storage 1-го поколения с помощью Azure Active Directory. Инструкции см. в разделе [Создание приложения Azure AD](../active-directory/develop/howto-create-service-principal-portal.md).

При выполнении инструкций по ссылке выше обязательно выберите тип приложения **Веб-приложение или API**, как показано на следующем снимке экрана:

![Создание веб-приложения](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Создание веб-приложения")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Шаг 2. Получение идентификатора приложения, ключ проверки подлинности и идентификатор клиента
При программном входе необходимо указывать идентификатор приложения. Если приложение работает с использованием собственных учетных данных, потребуется также ключ проверки подлинности.

* Инструкции о том, как получить идентификатор приложения и ключ проверки подлинности (который также называется секретом клиента) для приложения, см. в разделе [Получение идентификатора приложения и ключа проверки подлинности](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).

* Инструкции о том, как получить код клиента, см. в разделе [Получение идентификатора клиента](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Шаг 3. Назначение приложения Azure AD Gen1 хранилища Озера данных Azure учетной записи файла или папки


1. Выполните вход на [портал Azure](https://portal.azure.com). Откройте учетную запись Data Lake Storage 1-го поколения, которую необходимо связать с ранее созданным приложением Azure Active Directory.
2. В колонке учетной записи Data Lake Storage 1-го поколения щелкните **Azure Data Explorer**.
   
    ![Создание каталогов в учетной записи Data Lake Storage 1-го поколения](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Создание каталогов в учетной записи Data Lake")
3. В колонке **Обозреватель данных** щелкните файл (или папку), к которому необходимо предоставить доступ для приложения Azure AD, а затем нажмите кнопку **Доступ**. Чтобы настроить доступ к файлу, необходимо нажать кнопку **Доступ** в колонке **Предварительный просмотр файла**.
   
    ![Настройка списков управления доступом в файловой системе Data Lake](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Настройка списков управления доступом в файловой системе Data Lake")
4. В колонке **Доступ** перечислены стандартные и пользовательские варианты доступа, уже назначенные корню. Щелкните значок **Добавить** , чтобы добавить ACL пользовательского уровня.
   
    ![Перечисление стандартных и пользовательских сценариев доступа](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Перечисление стандартных и пользовательских сценариев доступа")
5. Щелкните значок **Добавить**, чтобы открыть колонку **Добавить настраиваемый доступ**. В этой колонке щелкните **Выберите пользователя или группу**, а затем в колонке **Выберите пользователя или группу** найдите созданное ранее приложение Azure Active Directory. Если у вас много групп для поиска, воспользуйтесь текстовым полем в верхней части для фильтрации по имени группы. Выберите группу, которую необходимо добавить, и нажмите кнопку **Выбрать**.
   
    ![Добавление группы](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Добавление группы")
6. Щелкните **Выберите разрешения**, выберите необходимые разрешения, а затем укажите, назначить ли разрешения как список ACL по умолчанию, список ACL для доступа или оба этих параметра. Последовательно выберите **ОК**.
   
    ![Назначение разрешений для группы](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Назначение разрешений для группы")
   
    Дополнительные сведения о разрешениях в Data Lake Storage 1-го поколения и списках ACL (по умолчанию и для доступа) см. в статье [Контроль доступа в Data Lake Storage 1-го поколения](data-lake-store-access-control.md).
7. В колонке **Добавить настраиваемый доступ** нажмите кнопку **ОК**. Теперь добавленная группа с соответствующими разрешениями отобразится в колонке **Доступ** .
   
    ![Назначение разрешений для группы](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Назначение разрешений для группы")

> [!NOTE]
> Если вы планируете ограничить свое приложение Azure Active Directory конкретной папкой, вам также потребуется предоставить этому приложению Azure Active Directory разрешение на **выполнение** в корневом каталоге, чтобы обеспечить доступ для создания файлов с помощью пакета SDK для .NET.

> [!NOTE]
> Если вы хотите использовать пакеты SDK для создания учетной записи Data Lake Storage 1-го поколения, необходимо назначить веб-приложение Azure AD в качестве роли группе ресурсов, в которой создается эта учетная запись.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Шаг 4. Получение конечной точки маркера OAuth 2.0 (только для приложений на основе Java)

1. Войдите на [портал Azure](https://portal.azure.com) и щелкните "Active Directory" в области слева.

2. В левой области щелкните **Регистрация приложений**.

3. В верхней части колонки "Регистрация приложений" щелкните **Конечные точки**.

    ![Конечная точка маркера OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "OAuth token endpoint")

4. Из списка конечных точек скопируйте значение конечной точки маркера OAuth 2.0.

    ![Конечная точка маркера OAuth](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "OAuth token endpoint")   

## <a name="next-steps"></a>Дальнейшие действия
В этой статье мы создали веб-приложение Azure AD и собрали сведения, необходимые в клиентских приложениях, создаваемых с помощью пакетов SDK для .NET, Java, Python, REST API и др. Из следующих статей вы узнаете, как с помощью собственного веб-приложения Azure AD проходить аутентификацию в Data Lake Storage 1-го поколения, а затем выполнить другие операции в хранилище.

* [Аутентификация между службами в Data Lake Storage 1-го поколения с помощью Java](data-lake-store-service-to-service-authenticate-java.md)
* [Аутентификация между службами в Data Lake Storage 1-го поколения с помощью пакета SDK для .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Аутентификация между службами в Data Lake Storage 1-го поколения с помощью Python](data-lake-store-service-to-service-authenticate-python.md)
* [Аутентификация между службами в Data Lake Storage 1-го поколения с помощью REST API](data-lake-store-service-to-service-authenticate-rest-api.md)


