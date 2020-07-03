---
title: Подключение приложения MongoDB к Azure Cosmos DB
description: Узнайте, как подключить приложение MongoDB к Azure Cosmos DB путем получения строки подключения из портал Azure
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/19/2020
ms.reviewer: sngun
ms.openlocfilehash: cba16d79b6506f9809a76f0128938a68afd15c92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80617052"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Подключение приложения MongoDB к Azure Cosmos DB

Здесь содержатся сведения о подключении приложения MongoDB к Azure Cosmos DB с помощью строки подключения MongoDB. Вы сможете использовать базу данных Azure Cosmos в качестве хранилища данных для приложения MongoDB.

В этом руководстве описаны два способа получения строки подключения.

- [Метод "Быстрый запуск"](#get-the-mongodb-connection-string-by-using-the-quick-start) для использования с драйверами .NET, Node.js, Java, Python или MongoDB.
- [Пользовательский метод строки соединения](#get-the-mongodb-connection-string-to-customize)для использования с другими драйверами

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure. Если у вас нет учетной записи, вы можете создать [бесплатную учетную запись Azure](https://azure.microsoft.com/free/).
- Учетная запись Cosmos. Инструкции см. в статье о [создании веб-приложения с помощью API Azure Cosmos DB для MongoDB и пакета SDK для .NET](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Получение строки подключения MongoDB с помощью метода быстрого запуска

1. В веб браузере Войдите в [портал Azure](https://portal.azure.com).
2. В колонке **Azure Cosmos DB** выберите API.
3. В левой панели в колонке учетной записи щелкните **Быстрый запуск**.
4. Выберите платформу (**.NET**, **Node.js**, **оболочка MongoDB**, **Java**, **Python**). Если соответствующего драйвера или средства нет в списке, не беспокойтесь, мы постоянно добавляем дополнительные фрагменты кода для подключения. Оставьте в конце статьи свои комментарии о том, что, по вашему мнению, нужно добавить. Дополнительные сведения о том, как создавать собственное подключение, см.в разделе [Получение строки подключения MongoDB для настройки](#get-the-mongodb-connection-string-to-customize).
5. Скопируйте и вставьте фрагмент кода в приложение MongoDB.

    ![Колонка "Быстрый запуск"](./media/connect-mongodb-account/QuickStartBlade.png)

## <a name="get-the-mongodb-connection-string-to-customize"></a> Получение строки подключения MongoDB для настройки

1. В веб браузере Войдите в [портал Azure](https://portal.azure.com).
2. В колонке **Azure Cosmos DB** выберите API.
3. На левой панели в колонке учетной записи щелкните **Строка подключения**.
4. Откроется колонка **Строка подключения**. Она содержит все необходимые сведения для подключения к учетной записи с помощью драйвера для MongoDB, включая автоматически сформированную строку подключения.

   [![Колонка](./media/connect-mongodb-account/ConnectionStringBlade.png) строки подключения](./media/connect-mongodb-account/ConnectionStringBlade.png#lightbox)

## <a name="connection-string-requirements"></a>Требования к строке подключения

> [!Important]
> В Azure Cosmos DB строгие требования к безопасности и стандарты. Для учетных записей Azure Cosmos DB требуется проверка подлинности и безопасная связь через *TLS*. 
>
>

Azure Cosmos DB поддерживает стандартный формат URI строки подключения MongoDB с несколькими особыми требованиями: Azure Cosmos DB учетных записей требуется проверка подлинности и безопасная связь через TLS. В этом случае строка подключения имеет следующий вид:

    mongodb://username:password@host:port/[database]?ssl=true

Значения для этой строки можно найти в колонке **Строка подключения**, как показано выше:

* Имя пользователя (обязательно): Cosmos учетной записи.
* Password (обязательно): пароль учетной записи Cosmos.
* Узел (обязательно): полное доменное имя учетной записи Cosmos.
* Порт (обязательно): 10255.
* База данных (необязательно): база данных, используемая для подключения. Если база данных не указана, база данных по умолчанию — test.
* ssl=true (обязательно)

Рассмотрим в качестве примера учетную запись, сведения о которой приведены выше в колонке **Строка подключения**. Допустимая строка подключения:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [использовать Studio 3T](mongodb-mongochef.md) с API Azure Cosmos DB для MongoDB.
- Узнайте, как [использовать Robo 3T](mongodb-robomongo.md) с API Azure Cosmos DB для MongoDB.
- Ознакомьтесь с [примерами](mongodb-samples.md) MongoDB с API Azure Cosmos DB для MongoDB.
