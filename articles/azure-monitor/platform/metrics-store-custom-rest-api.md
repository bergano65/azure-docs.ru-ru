---
title: Отправка метрик в базу данных метрик Azure Monitor с помощью REST API
description: Отправка пользовательских метрик для ресурса Azure в хранилище метрик Azure Monitor с использованием REST API
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: a19b59c758f31ff1ef3416b59031202193d50522
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285941"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Отправка пользовательских метрик для ресурса Azure в хранилище метрик Azure Monitor с использованием REST API

В этой статье показано, как отправлять пользовательские метрики для ресурсов Azure в хранилище метрик Azure Monitor с помощью REST API. Получив метрики в Azure Monitor, вы можете выполнять с ними любые действия, поддерживаемые для стандартных метрик. Например, создавать диаграммы или предупреждения и отправлять данные в другие внешние средства.  

>[!NOTE]  
>REST API позволяет отправлять пользовательские метрики только для ресурсов Azure. Чтобы отправлять метрики для ресурсов в разных средах или локально, можно использовать [Application Insights](../../azure-monitor/app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Создание и авторизация субъекта-службы для генерации метрик 

Создайте субъект-службу в клиенте Azure Active Directory, используя инструкции, приведенные в статье [Практическое руководство: Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../../active-directory/develop/howto-create-service-principal-portal.md). 

Выполняя этот процесс, учитывайте следующее: 

- Вы можете указать любой URL-адрес для входа в систему.  
- Создайте новый секрет клиента для этого приложения.  
- Сохраните ключ и идентификатор клиента, чтобы использовать их в дальнейшем.  

Предоставьте приложению издателя метрик мониторинга, которое вы создаете на шаге 1, доступ к ресурсу, для которого вам нужно генерировать метрики. Если вы планируете использовать приложение для генерации пользовательских метрик ко многим ресурсам, можно предоставить эти разрешения группе ресурсов или уровню подписки. 

## <a name="get-an-authorization-token"></a>Получение маркера авторизации
Откройте командную строку и выполните следующую команду:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Сохраните маркер доступа из ответа

![Маркер доступа](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Генерация метрики через REST API 

1. Вставьте приведенный ниже код JSON в файл и сохраните его с именем  **custommetric.json** на локальном компьютере. Обновите параметр времени в JSON-файле: 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. В окне командной строки опубликуйте данные метрики: 
   - **azureRegion.** Регион Azure, в котором развернут ресурс, для которого генерируются метрики. 
   - **resourceID.**  Идентификатор ресурса Azure, по которому вы отслеживаете метрику.  
   - **AccessToken.** Вставьте сюда маркер, полученный ранее.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Измените метку времени и значения в JSON-файле. 
1. Повторите предыдущие два шага несколько раз, чтобы у вас были данные за несколько минут.

## <a name="troubleshooting"></a>Устранение неполадок 
Если на любом этапе процесса появится сообщение об ошибке, изучите следующие сведения об устранении неполадок:

1. Вы не можете генерировать метрики для таких ресурсов Azure, как подписки или группы ресурсов. 
1. Вы не можете поместить в хранилище метрику, которая старше 20 минут. Хранилище метрик оптимизировано для оповещений и создания диаграмм в режиме реального времени. 
2. Число имен измерений должно соответствовать числу значений, и наоборот. Проверьте значения. 
2. Возможно, метрики генерируются для региона, в котором не поддерживаются пользовательские метрики. Просмотрите список [поддерживаемых регионов](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Просмотр метрик 

1. Войдите на портал Azure. 

1. В меню слева выберите **Монитор**. 

1. На странице **Монитор** щелкните **Метрики**. 

   ![Выбор метрик](./media/metrics-store-custom-rest-api/metrics.png) 

1. Измените период агрегирования на **Последние 30 минут**.  

1. В раскрывающемся списке **ресурс** выберите тот, по которому вы генерировали метрику.  

1. В раскрывающемся списке **Пространства имен** выберите **QueueProcessing** 

1. В раскрывающемся списке **метрик** выберите **QueueDepth**.  

 
## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о настраиваемых метриках см. в [этой статье](../../azure-monitor/platform/metrics-custom-overview.md).

