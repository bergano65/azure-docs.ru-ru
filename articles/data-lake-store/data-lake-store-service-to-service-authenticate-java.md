---
title: 'Проверки подлинности в службе: Java с помощью Gen1 хранилища Озера данных Azure с помощью Azure Active Directory | Документация Майкрософт'
description: Узнайте, как реализовать проверку подлинности между службами в Azure Data Lake Storage 1-го поколения с помощью Azure Active Directory и Java
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
ms.openlocfilehash: c32eada2acca73e089c2296ce8e59c529d7af665
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58879171"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Проверка подлинности между службами в Azure Data Lake Storage 1-го поколения с использованием Java
> [!div class="op_single_selector"]
> * [С использованием Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Использование пакета SDK для .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Использование Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Использование REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

В этой статье описывается, как использовать пакет Java SDK для проверки подлинности между службами с помощью Azure Data Lake Storage 1-го поколения. Проверка подлинности пользователей в Data Lake Storage 1-го поколения с помощью пакета Java SDK не поддерживается.

## <a name="prerequisites"></a>Технические условия
* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Создайте веб-приложение Azure Active Directory**. Вам нужно выполнить инструкции по [аутентификации между службами в Data Lake Storage 1-го поколения с помощью Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). В этом руководстве это средство используется для создания зависимостей проекта. Хотя зависимости можно создать и без использования таких систем, как Maven или Gradle, они существенно упрощают управление ими.

* Интегрированная среда разработки, например [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) или аналогичная (необязательно).

## <a name="service-to-service-authentication"></a>Взаимодействие между службами
1. Создайте проект Maven с использованием [архетипа mvn](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) с помощью командной строки или интегрированной среды разработки (IDE). Инструкции по созданию проекта Java с использованием IntelliJ см. [здесь](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Инструкции по созданию проекта с использованием Eclipse см. [здесь](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Добавьте приведенные ниже зависимости в файл Maven **pom.xml**. Добавьте следующий фрагмент перед тегом **\</project >**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    Первая зависимость предназначена для использования пакета SDK для Data Lake Storage 1-го поколения (`azure-data-lake-store-sdk`) из репозитория Maven. Вторая зависимость нужна, чтобы указать, какие платформы ведения журналов (`slf4j-nop`) будут использоваться для этого приложения. Пакет SDK для Data Lake Storage 1-го поколения использует библиотеку [slf4j](https://www.slf4j.org/), которая позволяет выбрать любую из ряда популярных платформ ведения журналов, таких как Log4j, платформа для Java, Logback и др., или отключить ведение журнала. В этом примере мы отключим ведение журнала, так как используем привязку **slf4j-nop**. Сведения о других вариантах ведения журнала в приложении см. [здесь](https://www.slf4j.org/manual.html#projectDep).

3. Добавьте следующие инструкции импорта в приложение.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. Используйте следующий фрагмент кода в приложении Java, чтобы получить маркер для веб-приложения Active Directory, созданного ранее с помощью одного из подклассов `AccessTokenProvider` (в следующем примере используется `ClientCredsTokenProvider`). Поставщик маркера кэширует в память учетные данные, которые использовались для получения маркера, и автоматически продлевает его истекающий срок действия. Кроме того, вы можете создать собственные подклассы `AccessTokenProvider`. В этом случае маркеры будут получать код вашего клиента. Здесь мы будем использовать подкласс из пакета SDK.

    Замените часть **FILL-IN-HERE** фактическими значениями, соответствующими веб-приложению Azure Active Directory.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

Пакет SDK для Data Lake Storage 1-го поколения предоставляет удобные методы управления маркерами безопасности, которые нужны для подключения к учетной записи Data Lake Storage 1-го поколения. Тем не менее пакет SDK не требует использования только этих методов. Можно использовать любые другие средства получения маркера, например [пакет SDK для Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) или пользовательский код.

## <a name="next-steps"></a>Дальнейшие действия
В этой статье вы узнали, как использовать проверку подлинности пользователей, чтобы реализовать проверку подлинности в Data Lake Storage 1-го поколения с помощью пакета Java SDK. Дополнительные сведения об использовании пакета Java SDK для работы с Data Lake Storage 1-го поколения см. в следующих статьях.

* [Операции с данными в Data Lake Storage 1-го поколения c использованием SDK для Java](data-lake-store-get-started-java-sdk.md)


