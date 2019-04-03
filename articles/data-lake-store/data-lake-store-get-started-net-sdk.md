---
title: Пакет SDK для .NET. Начало работы с Gen1 хранилища Озера данных Azure | Документация Майкрософт
description: Используйте пакет .NET SDK для Azure Data Lake Storage 1-го поколения, чтобы выполнять операции управления учетными записями в Azure Data Lake Storage 1-го поколения
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8ab051d49e7ed67e642ef656dfb382ed07763ed2
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879715"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Операции управления учетными записями в Azure Data Lake Storage 1-го поколения c использованием .NET SDK
> [!div class="op_single_selector"]
> * [ПАКЕТ SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

В этой статье содержатся сведения о выполнении операций управления учетными записями в Azure Data Lake Storage 1-го поколения с использованием .NET SDK. К операциям управления учетными записями относятся создание учетной записи Data Lake Storage 1-го поколения, перечисление учетных записей в подписке Azure, удаление учетных записей и т.д.

Дополнительные сведения о том, как выполнять операции управления данными в Data Lake Storage 1-го поколения с помощью .NET SDK, см. в статье [Операции файловой системы в Data Lake Storage 1-го поколения с использованием пакета SDK для .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Технические условия
* **Visual Studio 2013, 2015 или 2017**. В инструкциях ниже используется Visual Studio 2017.

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-net-application"></a>Создание приложения .NET
1. Откройте Visual Studio и создайте консольное приложение.
2. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.
3. В окне **Новый проект**введите или выберите следующие значения.

   | Свойство | Значение |
   | --- | --- |
   | Категория |Templates/Visual C#/Windows |
   | Шаблон |Консольное приложение |
   | ИМЯ |CreateADLApplication |
4. Нажмите кнопку **ОК** , чтобы создать проект.
5. Добавьте пакеты NuGet в проект.

   1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.
   2. На вкладке **Диспетчер пакетов NuGet** в поле **Источник пакета** выберите **nuget.org** и установите флажок **Включить предварительные выпуски**.
   3. Найдите и установите следующие пакеты NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` — В этом руководстве используется Предварительная версия 2.1.3.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` — В этом руководстве используется версия 2.2.12.

        ![Добавление источника NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Создание учетной записи Azure Data Lake")
   4. Закройте **диспетчер пакетов NuGet**.
6. Откройте файл **Program.cs**, удалите существующий код и включите следующие инструкции, чтобы добавить ссылки на пространства имен.

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

7. Объявите переменные и укажите значения вместо заполнителей. Кроме того, убедитесь, что локальный путь и имя файла, которые вы указываете, имеются на компьютере.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

В остальных разделах статьи рассматривается использование доступных методов .NET при выполнении таких операций, как проверка подлинности, отправка файла и т. д.

## <a name="authentication"></a>Authentication

* Дополнительные сведения о проверке подлинности пользователей в приложении см. в статье [Аутентификация пользователей в Data Lake Store с помощью пакета SDK для .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Дополнительные сведения о проверке подлинности между службами в приложении см. в статье [Аутентификация между службами в Data Lake Store с помощью пакета SDK для .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Создание клиентского объекта
В следующем фрагменте кода создается объект клиента учетной записи Data Lake Storage 1-го поколения, который используется для выдачи запросов управления учетной записью для службы, таких как создание учетной записи, удаление учетной записи и т. д.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Создание учетной записи Data Lake Storage 1-го поколения
В следующем фрагменте кода создается учетная запись Data Lake Storage 1-го поколения в подписке Azure, предоставленной при создании объекта клиента учетной записи Data Lake Storage 1-го поколения.

    // Create Data Lake Storage Gen1 account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Получение списка всех учетных записей Data Lake Storage 1-го поколения в рамках подписки
Добавьте в определение класса следующий метод. С помощью следующего фрагмента можно получить список всех учетных записей Data Lake Storage 1-го поколения в рамках определенной подписки Azure.

    // List all Data Lake Storage Gen1 accounts within the subscription
    public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccountBasic>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="delete-a-data-lake-storage-gen1-account"></a>Удаление учетной записи Data Lake Storage 1-го поколения
Следующий фрагмент кода удаляет учетную запись Data Lake Storage 1-го поколения, созданную ранее.

    // Delete Data Lake Storage Gen1 account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>См. также
* [Операции файловой системы в поколение 1 хранилища Озера данных с помощью пакета SDK для .NET](data-lake-store-data-operations-net-sdk.md)
* [Справочник по SDK .NET Gen1 хранилища Озера данных](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Дальнейшие действия
* [Защита данных в поколение 1 хранилища Озера данных](data-lake-store-secure-data.md)
