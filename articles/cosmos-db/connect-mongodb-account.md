---
title: Подключение приложения MongoDB к Azure Cosmos DB
description: Узнайте, как подключить приложение MongoDB к Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 737e179c2c16937d00bc9b6601f12ebe392c1906
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892541"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Подключение приложения MongoDB к Azure Cosmos DB
Здесь содержатся сведения о подключении приложения MongoDB к Azure Cosmos DB с помощью строки подключения MongoDB. Вы сможете использовать базу данных Azure Cosmos DB в качестве хранилища данных для приложения MongoDB. 

В этом руководстве описаны два способа получения строки подключения.

- [Метод "Быстрый запуск"](#QuickstartConnection) для использования с драйверами .NET, Node.js, Java, Python или MongoDB.
- [Метод с использованием пользовательской строки подключения](#GetCustomConnection) для других драйверов.

## <a name="prerequisites"></a>Технические условия

- Учетная запись Azure. Если у вас нет учетной записи, вы можете создать [бесплатную учетную запись Azure](https://azure.microsoft.com/free/). 
- Учетная запись Cosmos. Инструкции см. в статье о [создании веб-приложения с помощью API Azure Cosmos DB для MongoDB и пакета SDK для .NET](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Получение строки подключения MongoDB с помощью метода быстрого запуска
1. В браузере войдите на [портал Azure](https://portal.azure.com).
2. В колонке **Azure Cosmos DB** выберите API. 
3. В левой панели в колонке учетной записи щелкните **Быстрый запуск**. 
4. Выберите платформу (**.NET**, **Node.js**, **оболочка MongoDB**, **Java**, **Python**). Если соответствующего драйвера или средства нет в списке, не беспокойтесь, мы постоянно добавляем дополнительные фрагменты кода для подключения. Оставьте в конце статьи свои комментарии о том, что, по вашему мнению, нужно добавить. Дополнительные сведения о том, как создавать собственное подключение, см.в разделе [Получение строки подключения MongoDB для настройки](#GetCustomConnection).
5. Скопируйте и вставьте фрагмент кода в приложение MongoDB.

    ![Колонка "Быстрый запуск"](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Получение строки подключения MongoDB для настройки
1. В браузере войдите на [портал Azure](https://portal.azure.com).
2. В колонке **Azure Cosmos DB** выберите API. 
3. На левой панели в колонке учетной записи щелкните **Строка подключения**. 
4. Откроется колонка **Строка подключения**. Она содержит все необходимые сведения для подключения к учетной записи с помощью драйвера для MongoDB, включая автоматически сформированную строку подключения.

    ![Колонка "Строка подключения"](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Требования к строке подключения
> [!Important]
> В Azure Cosmos DB строгие требования к безопасности и стандарты. Для всех учетных записей Azure Cosmos DB требуется проверка подлинности и безопасный обмен данными через *SSL*. 
>
>

Azure Cosmos DB поддерживает стандартный формат URI для строки подключения MongoDB, налагая пару дополнительных условий. Для учетных записей Azure Cosmos DB требуется использовать проверку подлинности и безопасный обмен данными через SSL. В этом случае строка подключения имеет следующий вид:

    mongodb://username:password@host:port/[database]?ssl=true

Значения для этой строки можно найти в колонке **Строка подключения**, как показано выше:

* Имя пользователя (обязательно): имя учетной записи Cosmos DB.
* Пароль (обязательно): пароль учетной записи Cosmos.
* Узел (обязательно): полное доменное имя учетной записи Cosmos.
* Порт (обязательно): 10255.
* База данных (необязательно): база данных, используемая для подключения. Если база данных не указана, база данных по умолчанию — test.
* ssl=true (обязательно)

Рассмотрим в качестве примера учетную запись, сведения о которой приведены выше в колонке **Строка подключения**. Допустимая строка подключения:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [использовать Studio 3T](mongodb-mongochef.md) с API Azure Cosmos DB для MongoDB.
- Узнайте, как [использовать Robo 3T](mongodb-robomongo.md) с API Azure Cosmos DB для MongoDB.
- Ознакомьтесь с [примерами](mongodb-samples.md) MongoDB с API Azure Cosmos DB для MongoDB.
