---
title: Apache Kafka прокси-сервера RESTFUL — Azure HDInsight
description: Узнайте, как выполнять Apache Kafka операции с помощью прокси-сервера Kafka RESTFUL в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759000"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Взаимодействие с кластерами Apache Kafka в Azure HDInsight с помощью прокси-сервера RESTFUL

Прокси-сервер Kafka RESTFUL позволяет взаимодействовать с кластером Kafka через REST API по протоколу HTTP. Это действие означает, что клиенты Kafka могут находиться за пределами вашей виртуальной сети. Клиенты могут выполнять простые вызовы HTTP в кластере Kafka, не полагаясь на библиотеки Kafka. В этой статье показано, как создать кластер Kafka с поддержкой прокси-сервера RESTFUL. Также приводится пример кода, показывающий, как выполнять вызовы к прокси-серверу RESTFUL.

## <a name="rest-api-reference"></a>Справочник по REST API

Сведения о операциях, поддерживаемых REST API Kafka, см. в [справочнике по API-интерфейсу HDInsight Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>История

![Структура прокси-сервера Kafka RESTFUL](./media/rest-proxy/rest-proxy-architecture.png)

Полную спецификацию операций, поддерживаемых API, см. в разделе [Apache KAFKA API прокси-сервера RESTful](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Конечная точка прокси-сервера RESTFUL

При создании кластера HDInsight Kafka с прокси-сервером RESTFUL создается новая общедоступная конечная точка для кластера, которую можно найти в **свойствах** кластера HDInsight на портал Azure.

### <a name="security"></a>Безопасность

Доступ к прокси-серверу Kafka RESTFUL осуществляется с помощью групп безопасности Azure Active Directory. При создании кластера Kafka предоставьте группе безопасности Azure AD доступ к конечной точке RESTFUL. Клиенты Kafka, которым требуется доступ к прокси-серверу RESTFUL, должны быть зарегистрированы в этой группе владельцем группы. Владелец группы может зарегистрироваться через портал или с помощью PowerShell.

Для запросов конечной точки прокси-сервера RESTFUL клиентские приложения должны получить токен OAuth. Токен используется для проверки членства в группе безопасности. Ниже приведен [Пример клиентского приложения](#client-application-sample) , в котором показано, как получить маркер OAuth. Клиентское приложение передает маркер OAuth в запросе HTTP на прокси-сервер RESTFUL.

> [!NOTE]  
> Дополнительные сведения о группах безопасности AAD см. в статье [Управление доступом к приложениям и ресурсам с помощью групп Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md). Дополнительные сведения о работе маркеров OAuth см. в разделе [авторизация доступа к Azure Active Directory веб-приложениям с помощью потока предоставления кода OAuth 2,0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Предварительные условия

1. зарегистрировать приложение в Azure AD; Клиентские приложения, которые вы пишете для взаимодействия с прокси-сервером Kafka RESTFUL, будут использовать идентификатор и секрет приложения для проверки подлинности в Azure.

1. Создайте группу безопасности Azure AD. Добавьте приложение, зарегистрированное в Azure AD, в группу безопасности в качестве **члена** группы. Эта группа безопасности будет использоваться для управления тем, какие приложения могут взаимодействовать с прокси-сервером RESTFUL. Дополнительные сведения о создании групп Azure AD см. в статьях [Создание базовой группы и добавление членов с помощью Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Проверьте, что группа имеет тип " **Безопасность**".
    ![Группа безопасности](./media/rest-proxy/rest-proxy-group.png)

    Проверка того, что приложение является членом группы.
    ![Проверить членство](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Создание кластера Kafka с включенным прокси-сервером RESTFUL

1. Во время процесса создания кластера Kafka на вкладке **безопасность и сеть** установите флажок **включить прокси-сервер Kafka** .

     ![Включение прокси-сервера Kafka RESTFUL и выбор группы безопасности](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Щелкните **выбрать группу безопасности**. В списке групп безопасности выберите группу безопасности, которой требуется доступ к прокси-серверу RESTFUL. Для поиска соответствующей группы безопасности можно использовать поле поиска. Нажмите кнопку **выбрать** внизу.

     ![Включение прокси-сервера Kafka RESTFUL и выбор группы безопасности](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Выполните оставшиеся действия для создания кластера, как описано в статье [Создание кластера Apache Kafka в Azure HDInsight с помощью портал Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. После создания кластера перейдите к свойствам кластера, чтобы записать URL-адрес прокси-сервера Kafka.

     ![Просмотр URL-адреса прокси-сервера RESTFUL](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Пример клиентского приложения

Приведенный ниже код Python можно использовать для взаимодействия с прокси-сервером RESTFUL в кластере Kafka. Чтобы использовать пример кода, выполните следующие действия.

1. Сохраните пример кода на компьютере с установленным Python.
1. Установите необходимые зависимости Python, выполнив `pip3 install msal`.
1. Измените раздел Code (код), **Настройте эти свойства** и обновите следующие свойства для своей среды:

    |Свойство |Описание |
    |---|---|
    |Идентификатор клиента|Клиент Azure, на котором находится ваша подписка.|
    |ИД клиента|Идентификатор приложения, зарегистрированного в группе безопасности.|
    |Секрет клиента|Секрет для приложения, зарегистрированного в группе безопасности.|
    |Kafkarest_endpoint|Получите это значение на вкладке **Свойства** в обзоре кластера, как описано в [разделе Развертывание](#create-a-kafka-cluster-with-rest-proxy-enabled). Он должен иметь следующий формат:`https://<clustername>-kafkarest.azurehdinsight.net`|

1. В командной строке выполните файл Python, выполнив команду`sudo python3 <filename.py>`

Этот код выполняет следующее действие:

1. Извлекает токен OAuth из Azure AD.
1. Показывает, как выполнить запрос к Kafka прокси-серверу RESTFUL.

Дополнительные сведения о получении маркеров OAuth в Python см. в разделе [Python AuthenticationContext Class](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Вы можете увидеть задержку `topics` , которая не будет создана или удалена с помощью прокси-сервера Kafka RESTful. Эта задержка обусловлена обновлением кэша.

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

Ниже приведен еще один пример того, как получить маркер из Azure для прокси-сервера RESTFUL с помощью команды с фигурным признаком. **Обратите внимание, что `scope=https://hib.azurehdinsight.net/.default` нам требуется указанное время при получении маркера.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Дальнейшие шаги

* [Справочные документы по API-интерфейсу Kafka RESTFUL](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
