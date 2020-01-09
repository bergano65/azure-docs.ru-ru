---
title: Apache Kafka прокси-сервера RESTFUL — Azure HDInsight
description: Узнайте, как выполнять Apache Kafka операции с помощью прокси-сервера Kafka RESTFUL в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: a64d03ebe7c8bbb4cfa9c7bd63a678892250373d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482872"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Взаимодействие с кластерами Apache Kafka в Azure HDInsight с помощью прокси-сервера RESTFUL

Прокси-сервер Kafka RESTFUL позволяет взаимодействовать с кластером Kafka через REST API по протоколу HTTP. Это означает, что клиенты Kafka могут находиться за пределами виртуальной сети. Кроме того, клиенты могут выполнять простые HTTP-вызовы для отправки и получения сообщений в кластер Kafka, а не полагаться на библиотеки Kafka.  

## <a name="background"></a>Историческая справка

### <a name="architecture"></a>Архитектура

Без прокси-сервера RESTFUL клиенты Kafka должны находиться в той же виртуальной сети, что и кластер Kafka или одноранговая виртуальная сеть. Прокси-сервер RESTFUL позволяет подключать поставщиков данных или потребителей, расположенных где угодно. При развертывании прокси-сервера RESTFUL создается новая общедоступная конечная точка для кластера, которую можно найти в параметрах портала.

Полную спецификацию операций, поддерживаемых API, см. в разделе [Apache KAFKA API прокси-сервера RESTful](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="security"></a>Безопасность

Доступ к прокси-серверу Kafka RESTFUL осуществляется с помощью групп безопасности Azure Active Directory. Дополнительные сведения см. в статье [Управление доступом к приложениям и ресурсам с помощью групп Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups).

При создании кластера Kafka с включенным прокси-сервером RESTFUL вы предоставляете группу безопасности AAD, которая должна иметь доступ к конечной точке RESTFUL. Клиенты Kafka (приложения), которым требуется доступ к прокси-серверу RESTFUL, должны быть зарегистрированы в этой группе владельцем группы. Владелец группы может сделать это через портал или с помощью PowerShell.

Перед выполнением запросов к конечной точке прокси-сервера RESTFUL клиентское приложение должно получить маркер OAuth, чтобы проверить членство в правой группе безопасности. Дополнительные сведения о работе маркеров OAuth см. в разделе [авторизация доступа к Azure Active Directory веб-приложениям с помощью потока предоставления кода OAuth 2,0](../../active-directory/develop/v1-protocols-oauth-code.md). Пример получения маркера OAuth в Python см. в разделе [Пример клиентского приложения](#client-application-sample) .

После того как клиентское приложение будет иметь маркер OAuth, он должен передать этот маркер в HTTP-запросе, сделанном для прокси ОСТАЛЬной стороны.

## <a name="prerequisites"></a>Технические условия

1. зарегистрировать приложение в Azure AD; Клиентские приложения, которые вы пишете для взаимодействия с прокси-сервером Kafka RESTFUL, будут использовать идентификатор и секрет приложения для проверки подлинности в Azure.
1. Создайте группу безопасности Azure AD и добавьте приложение, зарегистрированное в Azure AD, в группу безопасности. Эта группа безопасности будет использоваться для управления тем, какие приложения могут взаимодействовать с прокси-сервером RESTFUL. Дополнительные сведения о создании групп Azure AD см. в статьях [Создание базовой группы и добавление членов с помощью Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Создание кластера Kafka с включенным прокси-сервером RESTFUL

1. На рабочем процессе создания кластера Kafka на вкладке "безопасность и сеть" установите флажок "включить прокси-сервер RESTFUL Kafka".

     ![Включение прокси-сервера Kafka RESTFUL и выбор группы безопасности](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-enable.png)

1. Щелкните **выбрать группу безопасности**. В списке групп безопасности выберите группу безопасности, которой требуется доступ к прокси-серверу RESTFUL. Для поиска соответствующей группы безопасности можно использовать поле поиска. Нажмите кнопку **выбрать** внизу.

     ![Включение прокси-сервера Kafka RESTFUL и выбор группы безопасности](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-select-security-group.png)

1. Выполните оставшиеся действия для создания кластера, как описано в статье [Создание кластера Apache Kafka в Azure HDInsight с помощью портал Azure](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. После создания кластера перейдите к свойствам кластера, чтобы записать URL-адрес прокси-сервера Kafka.

     ![Просмотр URL-адреса прокси-сервера RESTFUL](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Пример клиентского приложения

Приведенный ниже код Python можно использовать для взаимодействия с прокси-сервером RESTFUL в кластере Kafka. Этот код делает следующее:

1. Получение токена OAuth из Azure AD
1. Создает раздел, указанный
1. Отправляет сообщения в этот раздел
1. Использует сообщения из этого раздела

Дополнительные сведения о получении маркеров OAuth в Python см. в разделе [Python AuthenticationContext Class](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Вы можете столкнуться с задержкой, когда разделы, которые не были созданы или удалены с помощью прокси-сервера Kafka RESTFUL, отражаются там. Эта задержка вызвана обновлением кэша.

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

Чтобы использовать пример кода, выполните следующие действия.

1. Сохраните пример кода на компьютере с установленным Python.
1. Установите необходимые зависимости Python, выполнив `pip3 install adal` и `pip install msrestazure`.
1. Измените код и обновите следующие свойства для своей среды:
    1.  *Идентификатор клиента* — клиент Azure, на котором находится ваша подписка.
    1.  *Идентификатор клиента* — идентификатор приложения, зарегистрированного в группе безопасности.
    1.  *Секрет клиента* — секрет для приложения, зарегистрированного в группе безопасности.
    1.  *Kafkarest_endpoint* — получите это значение на вкладке "Свойства" в обзоре кластера, как описано в [разделе Развертывание](#create-a-kafka-cluster-with-rest-proxy-enabled). Он должен иметь следующий формат: `https://<clustername>-kafkarest.azurehdinsight.net`
3. В командной строке выполните файл Python, выполнив `python <filename.py>`

## <a name="next-steps"></a>Дальнейшие действия

* [Справочные документы по API-интерфейсу Kafka RESTFUL](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
* [Учебник. Использование API-интерфейсов производителя и потребителя Apache Kafka](apache-kafka-producer-consumer-api.md)