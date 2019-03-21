---
title: 'REST API: Начало работы с Gen1 хранилища Озера данных Azure | Документация Майкрософт'
description: Используйте Azure Data Lake Storage 1-го поколения и REST API WebHDFS, чтобы выполнять операции управления учетными записями в Data Lake Storage 1-го поколения
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: b2c4d38462cf617d8417b269d47a8c1bb0c266c3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530132"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Операции управления учетными записями в Azure Data Lake Storage 1-го поколения c использованием REST API
> [!div class="op_single_selector"]
> * [ПАКЕТ SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

В этой статье содержатся сведения о выполнении операций управления учетными записями в Azure Data Lake Storage 1-го поколения с использованием REST API. Операции управления учетными записями включают в себя создание учетной записи Data Lake Storage 1-го поколения, удаление учетной записи Data Lake Storage 1-го поколения и т. д. Дополнительные сведения о том, как выполнять операции файловой системы в Data Lake Storage 1-го поколения с помощью REST API, см. в [этой статье](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Технические условия
* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](https://curl.haxx.se/)**. В этой статье для демонстрации вызовов REST API к учетной записи Data Lake Storage 1-го поколения используется cURL.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Как выполнить аутентификацию с помощью Azure Active Directory?
Существует два способа проверки подлинности с помощью Azure Active Directory.

* Дополнительные сведения о проверке подлинности пользователей в приложении (интерактивная проверка подлинности) см. в статье [Аутентификация пользователей в Data Lake Store с помощью REST API](data-lake-store-end-user-authenticate-rest-api.md).
* Дополнительные сведения о проверке подлинности между службами в приложении (интерактивно) см. в статье [Проверка подлинности между службами в Data Lake Storage 1-го поколения с использованием пакета .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Создание учетной записи Data Lake Storage 1-го поколения
Эта операция основана на вызове REST API, определенном [здесь](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Используйте следующую команду cURL: Замените  **\<yourstoragegen1name>** именем своего хранилища Data Lake Storage 1-го поколения.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

В приведенной выше команде замените \<`REDACTED`\> полученным ранее маркером авторизации. Полезные данные запроса для этой команды находятся в файле **input.json**, предоставленном для параметра `-d` выше. Содержимое файла input.json выглядит как следующий фрагмент кода:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Удаление учетной записи Data Lake Storage 1-го поколения
Эта операция основана на вызове REST API, определенном [здесь](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete).

Чтобы удалить учетную запись Data Lake Storage 1-го поколения, используйте следующую команду cURL. Замените  **\<yourstoragegen1name>** именем своей учетной записи Data Lake Storage 1-го поколения.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Вы должны увидеть выходные данные, подобные следующему фрагменту кода:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Дальнейшие действия
* [Операции файловой системы в Data Lake Storage 1-го поколения c использованием REST API](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>См. также
* [Справочник по REST API для Azure Data Lake Storage 1-го поколения](https://docs.microsoft.com/rest/api/datalakestore/)
* [Приложения больших данных с открытым исходным кодом, которые работают с Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

