---
title: включить файл
description: включить файл
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 1235cb299fe887f20dd3f7e47c22eed2b9df6dc9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945737"
---
## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
- [комплект SDK для Java (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) версии 8 или более поздней версии.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Развернутый ресурс Служб коммуникации и строка подключения. [Создайте ресурс Служб коммуникации.](../create-communication-resource.md)

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-java-application"></a>Создание нового приложения Java

Откройте терминал или командное окно и перейдите в каталог, в котором нужно создать приложение Java. Выполните приведенную ниже команду, чтобы создать проект Java из шаблона maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Вы заметите, что задача generate создала каталог с тем же именем, что и у `artifactId`. В этом каталоге каталог src/main/java содержит исходный код проекта, `pom.xml` содержит источник теста, а файл `src/test/java directory` является объектной моделью проекта (POM).

### <a name="install-the-package"></a>Установка пакета

Откройте файл **pom.xml** в текстовом редакторе. Добавьте приведенный ниже элемент зависимости в группу зависимостей.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-administration</artifactId>
    <version>1.0.0-beta.1</version> 
</dependency>
```

### <a name="set-up-the-app-framework"></a>Настройка платформы приложения

Из каталога проекта:

1. Перейдите в каталог */src/main/java/com/communication/quickstart*.
1. Откройте файл *App.java* в редакторе.
1. Замените оператор `System.out.println("Hello world!");`.
1. Добавьте директивы `import`.

Используйте следующий код:

```java
import com.azure.communication.common.CommunicationUser;
import com.azure.communication.administration.models.CommunicationIdentityToken;
import com.azure.communication.administration.CommunicationIdentityClient;
import com.azure.communication.administration.CommunicationIdentityClientBuilder;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - User Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

Создайте экземпляр `CommunicationIdentityClient` с помощью ключа доступа и конечной точки ресурса. См. сведения о том, как [управлять строкой подключения ресурса](../create-communication-resource.md#store-your-connection-string).

Добавьте следующий код в метод `main`:

```java
// Your can find your endpoint and access token from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessToken = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationClientCredential(accessToken))
    .httpClient(httpClient)
    .buildClient();
```

Вы можете инициализировать клиент с помощью любого пользовательского HTTP-клиента, реализующего интерфейс `com.azure.core.http.HttpClient`. В приведенном выше коде показано, как использовать [HTTP-клиент Netty Azure Core](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true), предоставляемый `azure-core`.

## <a name="create-a-user"></a>Создание пользователя

Службы коммуникации Azure позволяют использовать упрощенный каталог удостоверений. Чтобы создать новую запись в каталоге с уникальным идентификатором (`Id`), используйте метод `createUser`. Необходимо обеспечить сопоставление между пользователями приложения и удостоверениями, созданными Службами коммуникации (например, храня удостоверения в базе данных сервера приложений).

```java
CommunicationUser user = communicationIdentityClient.createUser();
System.out.println("\nCreated a user with ID: " + user.getId());
```

## <a name="issue-user-access-tokens"></a>Выдача маркеров доступа пользователей

Чтобы выдать маркер доступа для пользователя Служб коммуникации, используйте метод `issueToken`. Если не указать необязательный параметр `user`, новый пользователь будет создан и возвращен вместе с маркером.

```java
// Issue an access token with the "voip" scope for a new user
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(user, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
String userId = response.getUser().getId();
System.out.println("\nIssued a access token with 'voip' scope for identity with ID: " + userId + ": " + token);
System.out.println(token);
```

Маркеры доступа пользователей — это кратковременные учетные данные, которые необходимо повторно выдавать, чтобы предотвратить сбои в работе служб. Свойство ответа `expiresAt` указывает на время существования маркера.

## <a name="revoke-user-access-tokens"></a>Отзыв маркеров доступа пользователей

Иногда может потребоваться явно отозвать маркеры доступа пользователя, например когда пользователь изменяет пароль, используемый для проверки подлинности в службе. При этом используется метод `revokeTokens`, чтобы сделать недействительными все маркеры доступа пользователя.

```java  
communicationIdentityClient.revokeTokens(user, OffsetDateTime.now());
System.out.println("\nRevoked tokens for the user with ID: " + user.getId());
```

## <a name="delete-a-user"></a>Удаление пользователя

При удалении пользователя отзываются все активные маркеры и запрещается выдача последующих маркеров для удостоверений. Вместе с этим удаляется и все хранимое содержимое, связанное с пользователем.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nSuccessfully deleted the identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>Выполнение кода

Перейдите в каталог, содержащий файл *pom.xml*, и скомпилируйте проект с помощью следующей команды `mvn`.

```console
mvn compile
```

Затем выполните сборку пакета.

```console
mvn package
```

Выполните следующую команду `mvn` для запуска приложения.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
