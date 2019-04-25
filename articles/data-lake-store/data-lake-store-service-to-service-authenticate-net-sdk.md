---
title: Аутентификация между службами в Azure Data Lake Storage 1-го поколения с помощью Azure Active Directory и пакета SDK для .NET | Документы Майкрософт
description: Узнайте, как реализовать аутентификацию между службами в Azure Data Lake Storage 1-го поколения с помощью Azure Active Directory и пакета SDK для .NET.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: be8d4172476ca0613e80e62739b9ab36f8ab4c3b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197753"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Аутентификация между службами в Azure Data Lake Storage 1-го поколения с помощью пакета SDK для .NET
> [!div class="op_single_selector"]
> * [С использованием Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Использование пакета SDK для .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Использование Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Использование REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

В этой статье описывается, как использовать пакет SDK для .NET для аутентификации между службами в Azure Data Lake Storage 1-го поколения. Дополнительные сведения об аутентификации пользователей в Azure Data Lake Storage 1-го поколения с помощью пакета SDK для .NET см. в статье [Аутентификация пользователя в Azure Data Lake Storage 1-го поколения с помощью пакета SDK для .NET](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Технические условия
* **Visual Studio 2013, 2015 или 2017**. В инструкциях ниже используется Visual Studio 2017.

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Создайте веб-приложение Azure Active Directory**. Вам нужно выполнить инструкции по [аутентификации между службами в Data Lake Storage 1-го поколения с помощью Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Создание приложения .NET
1. Откройте Visual Studio и создайте консольное приложение.
2. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.
3. В окне **Новый проект**введите или выберите следующие значения.

   | Свойство | Value |
   | --- | --- |
   | Category |Templates/Visual C#/Windows |
   | Шаблон |Консольное приложение |
   | Name |CreateADLApplication |
4. Нажмите кнопку **ОК** , чтобы создать проект.

5. Добавьте пакеты NuGet в проект.

   1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.
   2. На вкладке **Диспетчер пакетов NuGet** в поле **Источник пакета** выберите **nuget.org** и установите флажок **Включить предварительные выпуски**.
   3. Найдите и установите следующие пакеты NuGet:

      * `Microsoft.Azure.Management.DataLake.Store`. В этом руководстве используется предварительная версия 2.1.3.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`. В этом руководстве используется версия 2.2.12.

        ![Добавление источника NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Создание учетной записи Azure Data Lake")
   4. Закройте **диспетчер пакетов NuGet**.

6. Откройте файл **Program.cs**, удалите существующий код и включите следующие инструкции, чтобы добавить ссылки на пространства имен.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>Аутентификация между службами с помощью секрета клиента
Добавьте следующий фрагмент в клиентское приложение .NET. Замените значения заполнителей значениями, полученными в веб-приложении Azure AD (перечислено как предварительное условие). Этот фрагмент кода позволяет выполнять аутентификацию приложения **не в интерактивном режиме** в Data Lake Storage 1-го поколения с ключа или секрета клиента для веб-приложения Azure AD.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

В предыдущем фрагменте используется вспомогательная функция `GetCreds_SPI_SecretKey`. Код этой функции см. [на этой странице GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Аутентификация между службами с помощью сертификата

Добавьте следующий фрагмент кода в клиентское приложение .NET. Замените значения заполнителей значениями, полученными в веб-приложении Azure AD (перечислено как предварительное условие). Этот фрагмент кода позволяет выполнять аутентификацию приложения **не в интерактивном режиме** в Data Lake Storage 1-го поколения с помощью сертификата для веб-приложения Azure AD. Инструкции по созданию приложения Azure AD см. в руководстве по [созданию субъекта-службы с помощью сертификатов](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

В предыдущем фрагменте используется вспомогательная функция `GetCreds_SPI_Cert`. Код этой функции см. [на этой странице GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Дальнейшие действия
В этой статье описывается, как использовать аутентификацию между службами, чтобы реализовать аутентификацию в Data Lake Storage 1-го поколения с помощью пакета SDK для .NET. Дополнительные сведения об использовании пакета SDK для .NET для работы с Data Lake Storage 1-го поколения см. в следующих статьях.

* [Операции управления учетными записями в Data Lake Storage 1-го поколения c использованием пакета SDK для .NET](data-lake-store-get-started-net-sdk.md)
* [Операции с данными в Data Lake Storage 1-го поколения c использованием SDK для .NET](data-lake-store-data-operations-net-sdk.md)
