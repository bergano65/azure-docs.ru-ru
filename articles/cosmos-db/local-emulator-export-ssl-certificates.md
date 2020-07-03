---
title: Экспорт сертификатов эмулятора Azure Cosmos DB
description: При разработке на языках и средах выполнения, которые не используют хранилище сертификатов Windows, необходимо экспортировать сертификаты TLS/SSL и управлять ими. В этой статье приведены пошаговые инструкции.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.openlocfilehash: c72dbf24df850d8b0f7e5f26a873b78f5664c9e0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200944"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Экспорт сертификатов эмулятора Azure Cosmos DB для использования с Java, Python и Node.js

[**Скачать эмулятор**](https://aka.ms/cosmosdb-emulator)

Эмулятор Azure Cosmos DB предоставляет локальную среду, которая эмулирует службу Azure Cosmos DB для целей разработки, включая использование TLS-подключений. В этой записи показано, как экспортировать сертификаты TLS/SSL для использования на языках и средах выполнения, которые не интегрируются с хранилищем сертификатов Windows, например Java, в котором используется собственное [хранилище сертификатов](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) и Python, использующие [программы-оболочки сокетов](https://docs.python.org/2/library/ssl.html) и Node. js, использующие [тлссоккет](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Дополнительные сведения об эмуляторе см. в статье об [использовании эмулятора Azure Cosmos DB для разработки и тестирования](./local-emulator.md).

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * ротация сертификатов;
> * Экспорт сертификата TLS/SSL
> * использование сертификатов на Java, Python и Node.js.

## <a name="certification-rotation"></a>Смена сертификатов

Сертификаты в локальном эмуляторе Azure Cosmos DB создаются при первом его запуске. Создается два сертификата: один используется для подключения к локальному эмулятору, а второй — для управления секретами в нем. Вам нужно экспортировать сертификат подключения с именем DocumentDBEmulatorCertificate.

Оба сертификата можно повторно создать, щелкнув **команду сброса данных** в эмуляторе Azure Cosmos DB, запущенном на панели задач Windows, как показано ниже. Если вы повторно создали сертификаты и установили их в хранилище сертификатов Java или использовали их в другом месте, их необходимо обновить. В противном случае приложение не сможет подключиться к локальному эмулятору.

![Сброс данных в локальном эмуляторе Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Экспорт сертификата Azure Cosmos DB TLS/SSL

1. Запустите диспетчер сертификатов Windows, запустив certlm. msc и перейдя в папку Личные->Certificates, а затем откройте сертификат с понятным именем **DocumentDbEmulatorCertificate**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 1)](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Щелкните **Сведения**, а затем нажмите кнопку **ОК**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 2)](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Щелкните **Копировать в файл...**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 3)](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Щелкните **Далее**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 4)](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Щелкните **No, do not export private key** (Не экспортировать закрытый ключ), а затем — **Далее**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 5)](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Щелкните **Файлы X.509 (.CER) в кодировке Base-64**, а затем — **Далее**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 6)](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Укажите имя сертификата. В этом случае — **documentdbemulatorcert**. Щелкните **Далее**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 7)](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Нажмите кнопку **Готово**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 8)](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Как использовать сертификат в Java

При запуске приложений Java или MongoDB, использующих клиент Java, удобнее установить сертификат в хранилище сертификатов Java по умолчанию, чем передавать флаги `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"`. Например, включенное демонстрационное приложение Java`https://localhost:8081/_explorer/index.html`() зависит от хранилища сертификатов по умолчанию.

Следуйте инструкциям в статье [Добавление сертификата в хранилище сертификатов ЦС Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store), чтобы импортировать сертификат X.509 в хранилище сертификатов Java по умолчанию. Имейте в виду, что при использовании keytool вы будете работать в каталоге %JAVA_HOME%

После установки сертификата TLS/SSL "CosmosDBEmulatorCertificate" приложение должно иметь возможность подключения и использования локального эмулятора Azure Cosmos DB. Если по-прежнему возникают проблемы, вы можете следовать инструкциям в статье [Отладка подключений SSL/TLS](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) . Вероятно, сертификат не был установлен в хранилище %JAVA_HOME%/jre/lib/security/cacerts. Например, если вы установили несколько версий Java, приложение может использовать другое хранилище сертификатов, а не обновленное.

## <a name="how-to-use-the-certificate-in-python"></a>Как использовать сертификат в Python

По умолчанию [пакет SDK для Python (версия 2.0.0 или более поздней версии)](sql-api-sdk-python.md) для API SQL не будет пытаться использовать сертификат TLS/SSL при подключении к локальному эмулятору. Если вы хотите использовать проверку TLS, можно воспользоваться примерами в документации по [оболочкам сокетов Python](https://docs.python.org/2/library/ssl.html) .

## <a name="how-to-use-the-certificate-in-nodejs"></a>Как использовать сертификат в Node.js

По умолчанию [пакет SDK для Node. js (версия 1.10.1 или более поздней версии)](sql-api-sdk-node.md) для API SQL не будет пытаться использовать сертификат TLS/SSL при подключении к локальному эмулятору. Если вы хотите использовать проверку TLS, можно воспользоваться примерами в [документации по Node. js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * изменили сертификаты;
> * Экспорт сертификата TLS/SSL
> * узнали, как использовать сертификаты на Java, Python и Node.js.

Теперь можно перейти к разделу основных понятий, чтобы получить дополнительные сведения о службе Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Настраиваемые уровни согласованности данных в Azure Cosmos DB](../cosmos-db/consistency-levels.md)
