---
title: Apache Кафка REST прокси - Azure HDInsight
description: Узнайте, как выполнять операции Apache Kafka с помощью прокси-сервера Kafka REST на Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759000"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Взаимодействие с кластерами Apache Kafka в Azure HDInsight с помощью прокси REST

Прокси Kafka REST позволяет взаимодействовать с кластером Kafka через REST API через HTTP. Это действие означает, что ваши клиенты Kafka могут находиться за пределами вашей виртуальной сети. Клиенты могут совершать простые http-звонки в кластер Kafka, вместо того, чтобы полагаться на библиотеки Kafka. В этой статье будет показан способ создания кластера proxy REST с поддержкой Kafka. Также предоставляется пример кода, который показывает, как совершать звонки на прокси REST.

## <a name="rest-api-reference"></a>Справочник по REST API

Для операций, поддерживаемых API Kafka REST, [см.](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)

## <a name="background"></a>Историческая справка

![Дизайн прокси-сервера Kafka REST](./media/rest-proxy/rest-proxy-architecture.png)

Полную спецификацию операций, поддерживаемых API, можно узнать на [Apache Kafka REST Proxy API.](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)

### <a name="rest-proxy-endpoint"></a>КОНЕЧНая точка REST Прокси

Создание кластера HDInsight Kafka с помощью прокси-сервера REST создает новую общедоступную точку для кластера, которую можно найти в кластере HDInsight **Properties** на портале Azure.

### <a name="security"></a>Безопасность

Доступ к прокси-серверу Kafka REST управляется с помощью групп безопасности Azure Active Directory. При создании кластера Kafka предоставьте группе безопасности Azure AD доступ к конечным точкам REST. Клиенты Kafka, которым необходим доступ к прокси-серверу REST, должны быть зарегистрированы в этой группе владельцем группы. Владелец группы может зарегистрироваться через портал или через PowerShell.

Для запросов на конечную точку прокси-сервера REST клиентские приложения должны получить токен OAuth. Токен используется для проверки членства группы безопасности. Найдите [нижепример приложения Клиента,](#client-application-sample) который показывает, как получить токен OAuth. Клиентское приложение передает токен OAuth в запросе HTTP прокси REST.

> [!NOTE]  
> [См. Управление приложением и доступом к ресурсам с помощью групп Active Directory Azure,](../../active-directory/fundamentals/active-directory-manage-groups.md)чтобы узнать больше о группах безопасности AAD. Для получения дополнительной информации о том, [Authorize access to Azure Active Directory web applications using the OAuth 2.0 code grant flow](../../active-directory/develop/v1-protocols-oauth-code.md)как работают токены OAuth, см.

## <a name="prerequisites"></a>Предварительные требования

1. зарегистрировать приложение в Azure AD; Клиентские приложения, которые вы пишете для взаимодействия с прокси-сервером Kafka REST, будут использовать идентификатор этого приложения и секрет проверки подлинности в Azure.

1. Создайте группу безопасности Azure AD. Добавьте приложение, зарегистрированное в Azure AD, в группу безопасности в качестве **члена** группы. Эта группа безопасности будет использоваться для контроля, какие приложения могут взаимодействовать с прокси REST. Для получения дополнительной информации о создании групп Azure AD [см. Создать базовую группу и добавить участников с помощью Active Directory Azure.](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

    Проверка группы типа **Безопасности.**
    ![Группа безопасности](./media/rest-proxy/rest-proxy-group.png)

    Проверка этого приложения является членом Группы.
    ![Проверка членства](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Создание кластера Kafka с включенным прокси-сервером REST

1. Во время рабочего процесса создания кластера Kafka во вкладке **«Безопасность и сеть»** проверьте **прокси-опцию Enable Kafka REST.**

     ![Включить прокси-сервер Kafka REST и выбрать группу безопасности](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Нажмите **Выберите группу безопасности**. Из списка групп безопасности выберите группу безопасности, которая требует иметь доступ к прокси-серверу REST. Вы можете использовать поле поиска, чтобы найти соответствующую группу безопасности. Нажмите кнопку **«Выберите»** внизу.

     ![Включить прокси-сервер Kafka REST и выбрать группу безопасности](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Выполните оставшиеся шаги для создания кластера, описанного в [кластере Create Apache Kafka в Azure HDInsight с помощью портала Azure.](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)

1. Как только кластер создан, перейдите к свойствам кластера для записи прокси-URL Kafka REST.

     ![просмотр URL-адреса прокси REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Образец клиентского приложения

Вы можете использовать код питона ниже, чтобы взаимодействовать с прокси REST в кластере Kafka. Чтобы использовать образец кода, выполните следующие действия:

1. Сохраните пример кода на машине с установленным Python.
1. Установите требуемые зависимости `pip3 install msal`питона путем выполнения.
1. Измените раздел кода **Нанастройка этих свойств** и обновите следующие свойства для среды:

    |Свойство. |Описание |
    |---|---|
    |Tenant ID|Арендатор Azure, где находится ваша подписка.|
    |Идентификатор клиента|Идентификатор приложения, зарегистрированного в группе безопасности.|
    |Секрет клиента|Секрет приложения, которое вы зарегистрировали в группе безопасности.|
    |Kafkarest_endpoint|Получите это значение из вкладки **Свойства** в обзоре кластера, описанном в [разделе развертывания.](#create-a-kafka-cluster-with-rest-proxy-enabled) Она должна быть в следующем формате –`https://<clustername>-kafkarest.azurehdinsight.net`|

1. Из командной строки выполните файл python путем выполнения`sudo python3 <filename.py>`

Этот код выполняет следующее действие:

1. Получает токен OAuth из Azure AD.
1. Показывает, как сделать запрос на прокси Kafka REST.

Для получения дополнительной информации о получении токенов OAuth в python [см.](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python) Вы можете увидеть `topics` задержку, в то время как которые не созданы или удалены через прокси Kafka REST отражаются там. Эта задержка происходит из-за обновления кэша.

```python
#Required python packages
#pip3 install msal

import msal

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Ниже приведен другой пример того, как получить токен из Azure для прокси REST с помощью команды curl. **Обратите внимание, `scope=https://hib.azurehdinsight.net/.default` что нам нужно указанное при получении токена.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Следующие шаги

* [Справочные документы API по доверенности Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
