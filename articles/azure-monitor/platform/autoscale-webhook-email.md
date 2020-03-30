---
title: Использование автомасштабирования для отправки электронной почты и уведомлений об оповещениях веб-перехватчика
description: Узнайте, как использовать действия автомасштабного масштаба для вызова веб-URL или отправки уведомлений по электронной почте в Azure Monitor.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: c82b170bb3801bdc701ed84230db57f5691523ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120689"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Использование действий автомасштабирования для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Monitor
В этой статье показано, как настраиваются триггеры, позволяющие вам обращаться к определенным URL-адресам или отправлять сообщения электронной почты на основе действий автоматического масштабирования в Azure.  

## <a name="webhooks"></a>Веб-перехватчики
Объекты Webhook (веб-перехватчики) позволяют направлять уведомления об оповещениях Azure в другие системы для постобработки или отображения настраиваемых уведомлений. Например, направить оповещение службам, которые могут обрабатывать входящий веб-запрос для отправки SMS, ошибок в журнале, уведомления команды, использующие чат или службы обмена сообщениями, и т.д. Webhook URI должен быть действительным http или HTTPS конечная точка.

## <a name="email"></a>Email
Электронная почта может отправляться на любой допустимый адрес электронной почты. Также будут уведомляться администраторы и соадминистраторы подписки, где работает это правило.

## <a name="cloud-services-and-app-services"></a>Облачные службы и службы приложений
Вы можете зарегистрироваться на портале Azure для облачных служб и серверных ферм (App Services).

* Выберите метрику **scale by (масштабировать по)** .

![scale by (масштабировать по)](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Наборы для масштабирования виртуальных машин
Для новых виртуальных машин, созданных с помощью Resource Manager, наборы для масштабирования можно настроить с помощью REST API, шаблонов Resource Manager, PowerShell и интерфейса командной строки. Интерфейс портала еще недоступен.
При использовании шаблона REST API или Resource Manager включите элемент уведомлений в [автомасштабы](https://docs.microsoft.com/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) со следующими опциями.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| Поле | Обязательное? | Описание |
| --- | --- | --- |
| операции |да |Значение должно быть Scale. |
| sendToSubscriptionAdministrator |да |Значение должно быть true или false. |
| sendToSubscriptionCoAdministrators |да |Значение должно быть true или false. |
| customEmails |да |Значение может быть null или массивом строк с адресами электронной почты. |
| Объекты Webhook |да |Значение может быть null или допустимым универсальным кодом ресурса (URI). |
| serviceUri |да |Допустимый универсальный код ресурса (URI) HTTPS. |
| properties |да |Значение должно быть пустым, {}, или может содержать пары "ключ — значение". |

## <a name="authentication-in-webhooks"></a>Проверка подлинности в веб-перехватчиках
Для webhook может использоваться аутентификация на основе маркеров, заключающаяся в сохранении универсального кода ресурса (URI) webhook с идентификатором маркера в качестве параметра запроса. Например, https:\//mysamplealert/webcallback?tokenid'sometotokenid&someparameter-somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Схема полезных данных веб-перехватчика уведомлений автомасштабирования
При создании уведомлений автомасштабирования в полезные данные веб-перехватчика включаются следующие метаданные:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Поле | Обязательное? | Описание |
| --- | --- | --- |
| status |да |Состояние, которое указывает, что создано действие автоматического масштабирования |
| операции |да |Для увеличения экземпляров это будет "Развернуть", а для уменьшения экземпляров — "Свернуть" |
| контекст |да |Контекст действия автоматического масштабирования |
| TIMESTAMP |да |Отметка времени, когда было запущено действие автоматического масштабирования |
| идентификатор |Да |Идентификатор Resource Manager для параметра автоматического масштабирования |
| name |Да |Имя параметра автоматического масштабирования |
| подробности |Да |Описание действия, предпринятого службой автоматического масштабирования, и изменение числа экземпляров |
| subscriptionId |Да |Идентификатор подписки для масштабируемого целевого ресурса |
| имя_группы_ресурсов |Да |Имя группы ресурсов для масштабируемого целевого ресурса |
| resourceName |Да |Имя масштабируемого целевого ресурса |
| тип_ресурса |Да |Поддерживается три значения: "microsoft.classiccompute/domainnames/slots/roles" — роли облачной службы, "microsoft.compute/virtualmachinescalesets" — наборы для масштабирования виртуальных машин и "Microsoft.Web/serverfarms" — веб-приложение |
| resourceId |Да |Идентификатор Resource Manager для масштабируемого целевого ресурса |
| portalLink |Да |Ссылка на страницу сводки целевого ресурса на портале Azure |
| oldCapacity |Да |Текущее (старое) число экземпляров, когда автомасштабирование предпринимает действие масштабирования |
| newCapacity |Да |Новое число экземпляров, до которого автомасштабирование масштабирует ресурс |
| properties |нет |Необязательный параметр. Набор пар <ключ, значение> (например, Dictionary <String, String>). Поле свойства не является обязательным. В настраиваемом пользовательском интерфейсе или рабочем процессе на основе приложения логики вы можете вводить ключи и значения для передачи в виде полезных данных. Еще один способ передачи пользовательских свойств обратно в исходящий вызов веб-перехватчика — использование самого универсального кода ресурса (URI) веб-перехватчика (в виде параметров запроса). |

