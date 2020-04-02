---
title: Apache Кафка REST прокси - Azure HDInsight
description: Узнайте, как выполнять операции Apache Kafka с помощью прокси-сервера Kafka REST на Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 8997b385960c58b17747dfcfced74010af80550b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548217"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Взаимодействие с кластерами Apache Kafka в Azure HDInsight с помощью прокси REST

Прокси Kafka REST позволяет взаимодействовать с кластером Kafka через REST API через HTTP. Это означает, что ваши клиенты Kafka могут находиться за пределами вашей виртуальной сети. Кроме того, клиенты могут совершать простые http-звонки для отправки и приема сообщений в кластер Kafka, а не полагаться на библиотеки Kafka. Этот учебник покажет вам, как создать кластер REST прокси с поддержкой Kafka и предоставит пример кода, который показывает, как совершать звонки на прокси REST.

## <a name="rest-api-reference"></a>Справочник по REST API

Для полной спецификации операций, поддерживаемых API [HDInsight Kafka REST Proxy API Reference](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)Kafka REST, см.

## <a name="background"></a>Историческая справка

![Архитектура прокси-серверов Kafka REST](./media/rest-proxy/rest-proxy-architecture.png)

Для получения полной спецификации операций, [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)поддерживаемых API, пожалуйста, см.

### <a name="rest-proxy-endpoint"></a>КОНЕЧНая точка REST Прокси

Создание кластера HDInsight Kafka с прокси-сервером REST создает новую общедоступную точку для кластера, которую можно найти в кластере HDInsight "Свойства" на портале Azure.

### <a name="security"></a>Безопасность

Доступ к прокси-серверу Kafka REST управляется с помощью групп безопасности Azure Active Directory. При создании кластера Kafka с включенным прокси-сервером REST вы предоставите группу безопасности Active Directory Azure, которая должна иметь доступ к конечной точке REST. Клиенты Kafka (приложения), которым необходим доступ к прокси-серверу REST, должны быть зарегистрированы в этой группе владельцем группы. Владелец группы может сделать это через портал или через PowerShell.

Прежде чем делать запросы в конечную точку прокси REST, клиентское приложение должно получить токен OAuth для проверки членства в правильной группе безопасности. Найдите [нижепример приложения Клиента,](#client-application-sample) который показывает, как получить токен OAuth. После того, как клиентское приложение имеет токен OAuth, они должны передать этот токен в запросе HTTP, сделанном прокси REST.

> [!NOTE]  
> [См. Управление приложением и доступом к ресурсам с помощью групп Active Directory Azure,](../../active-directory/fundamentals/active-directory-manage-groups.md)чтобы узнать больше о группах безопасности AAD. Для получения дополнительной информации о том, [Authorize access to Azure Active Directory web applications using the OAuth 2.0 code grant flow](../../active-directory/develop/v1-protocols-oauth-code.md)как работают токены OAuth, см.

## <a name="prerequisites"></a>Предварительные требования

1. зарегистрировать приложение в Azure AD; Клиентские приложения, которые вы пишете для взаимодействия с прокси-сервером Kafka REST, будут использовать идентификатор этого приложения и секрет проверки подлинности в Azure.

1. Создайте группу безопасности Azure AD и добавьте приложение, зарегистрированное в Azure AD, в группу безопасности в качестве «члена» группы. Эта группа безопасности будет использоваться для контроля, какие приложения могут взаимодействовать с прокси REST. Для получения дополнительной информации о создании групп Azure AD [см. Создать базовую группу и добавить участников с помощью Active Directory Azure.](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

    Проверка группы типа "Безопасность" ![Security Group](./media/rest-proxy/rest-proxy-group.png)

    Проверка того, что ![приложение является участником членства в Group Validate](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Создание кластера Kafka с включенным прокси-сервером REST

1. Во время рабочего процесса создания кластера Kafka во вкладке "Безопасность и сеть" проверьте опцию "Включить прокси-сервер Kafka REST".

     ![Включить прокси-сервер Kafka REST и выбрать группу безопасности](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Нажмите **Выберите группу безопасности**. Из списка групп безопасности выберите группу безопасности, которая требует иметь доступ к прокси-серверу REST. Вы можете использовать поле поиска, чтобы найти соответствующую группу безопасности. Нажмите кнопку **«Выберите»** внизу.

     ![Включить прокси-сервер Kafka REST и выбрать группу безопасности](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Выполните оставшиеся шаги для создания кластера, описанного в [кластере Create Apache Kafka в Azure HDInsight с помощью портала Azure.](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)

1. Как только кластер создан, перейдите к свойствам кластера для записи прокси-URL Kafka REST.

     ![просмотр URL-адреса прокси REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Образец клиентского приложения

Вы можете использовать код питона ниже, чтобы взаимодействовать с прокси REST в кластере Kafka. Чтобы использовать образец кода, выполните следующие действия:

1. Сохраните пример кода на машине с установленным Python.
1. Установите требуемые зависимые `pip3 install adal` `pip install msrestazure`питоны путем выполнения и .
1. Измените раздел кода *Нанастройка этих свойств* и обновите следующие свойства для среды:
    1.    *Идентификатор арендатора* — арендатор Azure, где находится ваша подписка.
    1.    *Идентификатор клиента* — идентификатор приложения, зарегистрированного в группе безопасности.
    1.    *Секрет клиента* - Секрет приложения, которое вы зарегистрировали в группе безопасности
    1.    *Kafkarest_endpoint* - получить это значение из вкладки "свойства" в обзоре кластера, описанном в [разделе развертывания.](#create-a-kafka-cluster-with-rest-proxy-enabled) Она должна быть в следующем формате –`https://<clustername>-kafkarest.azurehdinsight.net`
1. Из командной строки выполните файл python путем выполнения`python <filename.py>`

Этот код делает следующее:

1. Получает токен OAuth из Azure AD
1. Показывает, как сделать запрос на прокси Кафка REST

Для получения дополнительной информации о получении токенов OAuth в python [см.](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python) Вы можете увидеть задержку, в то время как темы, которые не созданы или удалены через прокси Kafka REST, отражаются там. Эта задержка связана с обновлением кэша.

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

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

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Ниже приведен другой пример того, как получить токен из Azure для прокси REST с помощью команды curl. Обратите внимание, `resource=https://hib.azurehdinsight.net` что нам нужно указанное при получении токена.

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&resource=https://hib.azurehdinsight.net' 'https://login.microsoftonline.com/<tenantid>/oauth2/token'
```

## <a name="next-steps"></a>Дальнейшие действия

* [Справочные документы API по доверенности Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
