---
title: Использование Анализ текста для работоспособности
titleSuffix: Azure Cognitive Services
description: Узнайте, как извлекать и помечать медицинские сведения из неструктурированного текста клинической практике с Анализ текста для работоспособности.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: d9517eef8976e79db21fbe552861d0d59923e8ba
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173949"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Как использовать Анализ текста для работоспособности (Предварительная версия)

> [!NOTE]
> Анализ текста для контейнера работоспособности недавно обновлялись. Дополнительные сведения о последних изменениях см. в разделе [новые](../whats-new.md) возможности. Не забудьте извлечь последний контейнер для использования перечисленных обновлений.

> [!IMPORTANT] 
> Анализ текста для работоспособности — это возможность предварительной версии, предоставленная "как есть" и "со всеми ОШИБКАми". Таким образом, **анализ текста для работоспособности (Предварительная версия) не следует реализовывать и развертывать в любом рабочем использовании.** Анализ текста для обеспечения работоспособности не предназначены для использования в качестве медицинских устройств, поддержки клинической практике, диагностического средства или других технологий, предназначенных для диагностики, получения, устранения, обработки или предотвращения болезни или других условий, и ни одна из лицензий или прав не предоставляется корпорацией Майкрософт для использования этой возможности в таких целях. Эта возможность не предназначена для реализации или развертывания в качестве замены для профессиональных медицинских консультаций или здравоохранения, диагностики, лечения или клинической практике, которые являются нежелательными специалистами здравоохранения и не должны использоваться. Клиент несет ответственность за использование Анализ текста для работоспособности. Корпорация Майкрософт не гарантирует, что Анализ текста для обеспечения работоспособности или каких бы то ни было материалов, предоставляемых в связи с этой возможностью, будет достаточно для любых медицинских целей или иным образом соответствовать требованиям или медицинских нужд любого человека. 


Анализ текста для работоспособности — это Контейнерная служба, которая извлекает и помечает соответствующие медицинские сведения из неструктурированных текстов, таких как заметки Doctor, сводки разрядов, документы клинической практике и электронные записи о работоспособности.  

## <a name="features"></a>Компоненты

Анализ текста для контейнера работоспособности в настоящее время выполняет распознавание имен сущностей (NER), извлечение отношений, отрицание сущностей и связывание сущностей для английского языка в собственной среде разработки, которая соответствует вашим требованиям к безопасности и управлению данными.

#### <a name="named-entity-recognition"></a>[Распознавание именованных сущностей](#tab/ner)

Распознавание именованных сущностей обнаруживает слова и фразы, упомянутые в неструктурированном тексте, которые могут быть связаны с одним или несколькими семантическими типами, такими как диагностика, имя лечения, симптом, подпись или возраст.

> [!div class="mx-imgBorder"]
> ![NER работоспособности](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[Извлечение связей](#tab/relation-extraction)

Извлечение связей определяет осмысленные соединения между понятиями, упомянутыми в тексте. Например, связь "время условия" обнаруживается путем связывания имени условия с временем. 

> [!div class="mx-imgBorder"]
> ![Повтор работоспособности](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[Связывание сущностей](#tab/entity-linking)

Сущность, связывающая сущности различения, сопоставляя именованные сущности, упомянутые в тексте, с концепциями, найденными в предопределенной базе данных концепций. Например, единая медицинское языковая система (УМЛС).

> [!div class="mx-imgBorder"]
> ![Работоспособность EL](../media/ta-for-health/health-entity-linking.png)

Анализ текста для работоспособности поддерживает связывание с работоспособностью и биомедицинских словари, найденных в источнике знаний об метасесаурусе единой системы обработки медицинских языков ([умлс](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)).

#### <a name="negation-detection"></a>[Обнаружение отрицания](#tab/negation-detection) 

Значение медицинских материалов сильно влияет на модификаторы, такие как отрицание, которое может иметь критически важное значение при неточной диагностике. Анализ текста для работоспособности поддерживает обнаружение отрицания для различных сущностей, упомянутых в тексте. 

> [!div class="mx-imgBorder"]
> ![РАСХОД работоспособности](../media/ta-for-health/health-negation.png)

---

Полный список поддерживаемых сущностей см. в статье [категории сущностей](../named-entity-types.md?tabs=health) , возвращенных анализ текста.

## <a name="supported-languages"></a>Поддерживаемые языки

Анализ текста для работоспособности поддерживает только документы на английском языке.

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Заполните и отправьте [форму запроса Cognitive Services контейнеров](https://aka.ms/csgate) , чтобы запросить доступ к контейнеру. В настоящее время вам не будет выставлен счет за Анализ текста для использования работоспособности. 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>Установка контейнера

Существует несколько способов установки и запуска контейнера. 

- Используйте [портал Azure](text-analytics-how-to-install-containers.md?tabs=healthcare) , чтобы создать анализ текста ресурс, и используйте DOCKER для получения контейнера.
- Используйте следующие сценарии PowerShell и [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) для автоматизации конфигурации контейнера развертывания ресурсов.

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Установка контейнера с помощью Azure Веб-приложение для контейнеров

Azure [веб-приложение для контейнеров](https://azure.microsoft.com/services/app-service/containers/) — это ресурс Azure, предназначенный для выполнения контейнеров в облаке. Она предоставляет готовые возможности, такие как автоматическое масштабирование, поддержка контейнеров DOCKER и создание прикрепления DOCKER, Поддержка HTTPS и многое другое.

> [!NOTE]
> С помощью веб-приложения Azure вы автоматически получите домен в виде `<appservice_name>.azurewebsites.net`

Запустите этот скрипт PowerShell с помощью Azure CLI, чтобы создать Веб-приложение для контейнеров, используя подписку и образ контейнера по протоколу HTTPS. Дождитесь завершения сценария (приблизительно 25-30 минут) перед отправкой первого запроса.

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Установка контейнера с помощью экземпляра контейнера Azure

Вы также можете использовать экземпляр контейнера Azure (ACI), чтобы упростить развертывание. ACI — это ресурс, который позволяет запускать контейнеры DOCKER по запросу в управляемой среде Azure, не поддерживающей работу сервера. 

Инструкции по развертыванию ресурса ACI с помощью портал Azure см. в статье Использование службы " [экземпляры контейнеров Azure](text-analytics-how-to-use-container-instances.md) ". Вы также можете использовать приведенный ниже сценарий PowerShell с помощью Azure CLI, который создаст ACI в подписке с помощью образа контейнера.  Дождитесь завершения сценария (приблизительно 25-30 минут) перед отправкой первого запроса.  Из-за ограничения на максимальное число ЦП на ресурс ACI не выбирайте этот параметр, если предполагается отправлять более 5 больших документов (примерно 5000 символов каждый) для каждого запроса.
Сведения о доступности см. в статье [ACI региональные службы поддержки](https://docs.microsoft.com/azure/container-instances/container-instances-region-availability) . 

> [!NOTE] 
> В службе "экземпляры контейнеров Azure" не включена поддержка HTTPS для встроенных доменов. Если требуется протокол HTTPS, необходимо настроить его вручную, включая создание сертификата и регистрацию домена. Инструкции для этого можно найти в NGINX ниже.

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Безопасное подключение ACI

По умолчанию при использовании ACI с API контейнера безопасность не предоставляется. Это связано с тем, что обычно контейнеры будут выполняться как часть модуля, который защищен от внешней сети сетевым мостом. Однако можно изменить контейнер с интерфейсным компонентом, сохранив конечную точку контейнера закрытой. В следующих примерах используется [nginx](https://www.nginx.com) в качестве входящего шлюза для поддержки проверки подлинности по протоколам HTTPS/SSL и Client-Certificate.

> [!NOTE]
> NGINX — это высокопроизводительный и прокси-сервер HTTP с открытым исходным кодом. Контейнер NGINX можно использовать для завершения подключения TLS для одного контейнера. Также возможны более сложные решения завершения TLS на основе NGINX.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Настройка NGINX в качестве входящего шлюза

NGINX использует [файлы конфигурации](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) для включения компонентов во время выполнения. Чтобы включить завершение TLS для другой службы, необходимо указать SSL-сертификат для завершения подключения TLS и  `proxy_pass` указать адрес службы. Пример приведен ниже.


> [!NOTE]
> `ssl_certificate` в локальной файловой системе контейнера NGINX требуется указать путь. Адрес, указанный для, `proxy_pass` должен быть доступен в сети контейнера nginx.

Контейнер NGINX загрузит все файлы в `_.conf_` , которые будут подключены к `/etc/nginx/conf.d/` пути конфигурации HTTP.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Пример файла создания DOCKER

В приведенном ниже примере показано, как можно создать файл- [сокомпозицию DOCKER](https://docs.docker.com/compose/reference/overview) для развертывания NGINX и анализ текста для контейнеров работоспособности.

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Чтобы инициировать этот файл создания DOCKER, выполните следующую команду в консоли на корневом уровне файла:

```bash
docker-compose up
```

Дополнительные сведения см. в документации по NGINX на [nginx SSL](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).


## <a name="example-api-request"></a>Пример запроса к API
Контейнер предоставляет интерфейсы REST API конечной точки прогнозирования запросов.  Мы также предоставили средство визуализации в контейнере, доступ к которому можно получить, добавив **демонстрацию** в конечную точку контейнера, например:

```bash
http://<serverURL>:5000/demo
```

Используйте приведенный ниже запрос в виде фигурной скобки, чтобы отправить запрос в развернутый контейнер, заменив `serverURL` переменную соответствующим значением.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Ниже приведен пример JSON-файла, присоединенного к Анализ текста в теле запроса API работоспособности:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>Текст ответа API

Следующий код JSON является примером Анализ текста для текста ответа API работоспособности:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "category": "SymptomOrSign",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "category": "Frequency",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-07-24"
}
```

### <a name="negation-detection-output"></a>Вывод обнаружения отрицания

При использовании обнаружения отрицания в некоторых случаях один термин отрицания может одновременно обращаться к нескольким терминам. Отрицание распознанной сущности представлено в выходных данных JSON по логическому значению `isNegated` флага:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Выход извлечения связи

Выход извлечения связи содержит ссылки URI на *источник* связи и его *целевой объект*. Сущности с ролью отношения `ENTITY` принадлежат `target` полю. Сущности с ролью отношения `ATTRIBUTE` принадлежат `source` полю. Связи аббревиатур содержат двунаправленную форму `source` и `target` поля, `bidirectional` для которых будет задано значение `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>См. также раздел

* [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)
* [Категории именованных сущностей](../named-entity-types.md)
* [Новые возможности](../whats-new.md)
