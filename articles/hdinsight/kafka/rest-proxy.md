---
title: Прокси-сервер REST для Apache Kafka (Azure HDInsight)
description: Сведения о том, как выполнять в Azure HDInsight операции Apache Kafka с помощью прокси-сервера REST для Kafka.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: 660e200b673da53af1ee00e4de1e2ce3298e861d
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876450"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Взаимодействие с кластерами Apache Kafka в Azure HDInsight через прокси-сервер REST

Прокси-сервер REST для Kafka позволяет взаимодействовать с кластером Kafka через REST API по протоколу HTTP. Это действие означает, что клиенты Kafka могут находиться за пределами виртуальной сети. Клиенты смогут выполнять простые вызовы HTTP к кластеру Kafka, не используя библиотеки Kafka. В этой статье показано, как создать кластер Kafka с поддержкой прокси-сервера REST. Также вы получите пример кода для вызовов к прокси-серверу REST.

## <a name="rest-api-reference"></a>Справочник по REST API

Сведения об операциях, поддерживаемых REST API Kafka, см. в [справочнике по API прокси-сервера REST для Kafka в Azure HDInsight](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Историческая справка

![Архитектура прокси-сервера REST для Kafka](./media/rest-proxy/rest-proxy-architecture.png)

Полную спецификацию операций, поддерживаемых API прокси-сервера REST для Apache Kafka, см. [здесь](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Конечная точка прокси-сервера REST

При создании кластера Kafka в HDInsight с прокси-сервером REST для этого кластера создается новая общедоступная конечная точка, которую можно найти на странице **Свойства** для кластера HDInsight на портале Azure.

### <a name="security"></a>Безопасность

Доступом к прокси-серверу REST для Kafka можно управлять через группы безопасности Azure Active Directory. При создании кластера Kafka предоставьте группе безопасности AAD доступ к конечной точке REST. Клиенты Kafka, которым нужен доступ к прокси-серверу REST, должны быть зарегистрированы в этой группе с ролью владельца. Владелец группы может зарегистрироваться через портал или с помощью PowerShell.

Для запросов к конечной точке прокси-сервера REST клиентским приложениям нужно получить токен OAuth. Этот же токен используется для проверки членства в группе безопасности. Изучите ниже [пример клиентского приложения](#client-application-sample), в котором демонстрируется получение токена OAuth. Клиентское приложение передает этот токен OAuth прокси-серверу REST в запросе HTTP.

> [!NOTE]
> В статье [Управление доступом к приложениям и ресурсам с помощью групп Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md) вы найдете дополнительные сведения о группах безопасности AAD. Дополнительные сведения о работе токенов OAuth см. в статье [Авторизация доступа к веб-приложениям Azure Active Directory с помощью потока предоставления кода OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Работа прокси-сервера REST для Kafka с группами безопасности сети
Если вы используете собственную виртуальную сеть и управляете сетевым трафиком с помощью групп безопасности сети, разрешите **входящий трафик** через порт **9400** в дополнение к стандартному порту 443. Это обеспечит доступность прокси-сервера REST для Kafka.

## <a name="prerequisites"></a>Предварительные требования

1. зарегистрировать приложение в Azure AD; Клиентские приложения, которые вы создаете для взаимодействия с прокси-сервером REST для Kafka, будут использовать эти значения идентификатора и секрета приложения для проверки подлинности в Azure.

1. Создайте группу безопасности Azure AD. Добавьте приложение, которое вы зарегистрировали в Azure AD, в группу безопасности в качестве **члена** группы. Эта группа безопасности будет использоваться для управления тем, какие приложения могут взаимодействовать с прокси-сервером REST. Ознакомьтесь со статьей [Создание простой группы и добавление в нее участников с помощью Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md), чтобы получить сведения о создании групп AAD.

    Убедитесь, что для этой группы установлен тип **Безопасность**.
    ![Группа безопасности](./media/rest-proxy/rest-proxy-group.png)

    Убедитесь, что приложение является членом группы.
    ![Проверка членства](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Создание кластера Kafka с включенным прокси-сервером REST

Для описанных ниже действий используется портал Azure. Пример работы с Azure CLI см. в статье [Создание кластера Apache Kafka с прокси-сервером REST с помощью Azure CLI](tutorial-cli-rest-proxy.md).

1. В процессе создания кластера Kafka установите на вкладке **Безопасность и сеть** флажок **Включить прокси-сервер REST для Kafka**.

     ![Включение прокси-сервера REST для Kafka и выбор группы безопасности](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Щелкните **Выбор группы безопасности**. Из предложенного списка выберите ту группу безопасности, которая должна иметь доступ к прокси-серверу REST. Чтобы найти нужную группу безопасности, можно воспользоваться полем поиска. В нижней части страницы нажмите кнопку **Выбрать**.

     ![Включение прокси-сервера REST для Kafka и выбор группы безопасности](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Выполните остальные действия для создания кластера, как описано в статье [Создание кластера Apache Kafka в Azure HDInsight с помощью портала Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. После создания кластера перейдите к свойствам кластера, чтобы записать URL-адрес прокси-сервера REST для Kafka.

     ![Просмотр URL-адреса прокси-сервера REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Пример клиентского приложения

Приведенный ниже код Python можно использовать для взаимодействия с прокси-сервером REST в кластере Kafka. Чтобы использовать этот пример кода, выполните следующие шаги:

1. Сохраните пример кода на компьютере, где установлен Python.
1. Установите необходимые зависимости Python, выполнив команду `pip3 install msal`.
1. Измените раздел **Configure these properties** (Настройте эти свойства) в коде, указав для следующих свойства значения из вашей среды:

    |Свойство |Описание |
    |---|---|
    |Tenant ID|Клиент Azure, на котором находится ваша подписка.|
    |Идентификатор клиента|Идентификатор приложения, зарегистрированного в группе безопасности.|
    |Секрет клиента|Секрет приложения, зарегистрированного в группе безопасности.|
    |Kafkarest_endpoint|Получите это значение на вкладке **Свойства** в разделе сводной информации о кластере, как описано в [разделе о развертывании](#create-a-kafka-cluster-with-rest-proxy-enabled). Оно должно быть указано в формате `https://<clustername>-kafkarest.azurehdinsight.net`.|

1. В командной строке выполните файл Python с помощью команды `sudo python3 <filename.py>`.

Этот код делает следующее:

1. Получает токен OAuth из Azure AD.
1. Демонстрирует выполнение запроса к прокси-серверу REST для Kafka.

Дополнительные сведения о получении токенов OAuth в Python см. в [описании класса AuthenticationContext в Python](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Вы можете заметить некоторую задержку в отображении состояния `topics` при создании или удалении не через прокси-сервер REST для Kafka. Она обусловлена обновлением кэша.

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

Ниже приведен еще один пример того, как можно получить токен из Azure для прокси-сервера REST с помощью команды curl. **Обратите внимание, что при получении токена нужно указать `scope=https://hib.azurehdinsight.net/.default`.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Дальнейшие действия

* [Справочные документы по API прокси-сервера REST для Kafka](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
