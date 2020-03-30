---
title: Устойчивое состояние в Linux - Azure Event Grid IoT Edge Документы Майкрософт
description: Устойчивые метаданные в Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086655"
---
# <a name="persist-state-in-linux"></a>Устойчивое состояние в Linux

Темы и подписки, созданные в модуле Event Grid, хранятся в файловой системе контейнеров по умолчанию. Без настойчивости, если модуль будет передислоцирован, все созданные метаданные будут потеряны. Чтобы сохранить данные при развертывании и перезагрузке, необходимо сохранять данные за пределами файловой системы контейнеров.

По умолчанию сохраняются только метаданные, а события по-прежнему хранятся в памяти для повышения производительности. Следуйте разделу упорствусобытий, чтобы также обеспечить сохранение событий.

В этой статье приводятся шаги по развертыванию модуля Event Grid с сохранением в развертывании Linux.

> [!NOTE]
>Модуль Event Grid работает как низкопривилегированный пользователь `2000` с `eventgriduser`UID и именем.

## <a name="persistence-via-volume-mount"></a>Настойчивость через объемкрепления

 [Объемы докеров](https://docs.docker.com/storage/volumes/) используются для сохранения данных в развертывании. Вы можете позволить докеру автоматически создавать именный том в рамках развертывания модуля Event Grid. Этот вариант является самым простым вариантом. Можно указать имя громкости, которое будет создано в разделе **Binds** следующим образом:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Не изменяйте вторую часть значения связывания. Он указывает на определенное место в модуле. Для модуля Event Grid на Linux, он должен быть **/app/metadataDb**.

Например, следующая конфигурация приведет к созданию тома **egmetadataDbVol,** где метаданные будут сохраняться.

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Вместо установки тома можно создать каталог в системе хоста и смонтировать этот каталог.

## <a name="persistence-via-host-directory-mount"></a>Настойчивость через хост-каталогьного монтажа

Вместо громкости докера у вас также есть возможность смонтировать папку-хоста.

1. Сначала создайте пользователя с именем **eventgriduser** и ID **2000** на хост-машине, запустив следующую команду:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Создайте каталог в файловой системе хоста, запустив следующую команду.

   ```sh
   md <your-directory-name-here>
   ```

    Например, запуск следующей команды создаст каталог под названием **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Затем сделайте владелец этой папки **eventgriduser,** запустив следующую команду.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Например,

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Используйте **Binds** для установки каталога и передислокации модуля Event Grid с портала Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    Например,

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >Не изменяйте вторую часть значения связывания. Он указывает на определенное место в модуле. Для модуля Event Grid на linux, он должен быть **/app/metadataDb** и **/app/eventsDb**


## <a name="persist-events"></a>Устойчивые события

Для обеспечения сохранения событий необходимо сначала включить сохранение метаданных либо с помощью крепления громкости, либо крепления каталога хоста с использованием вышеуказанных разделов.

Важные вещи, которые следует отметить в отношении сохраняющихся событий:

* Упорные события включены на основе подписки событий и выбираются после установки тома или каталога.
* Сохранение событий настраивается на подписку событий во время создания и не может быть изменено после создания подписки на событие. Чтобы переключить настойчивость событий, необходимо удалить и воссоздать подписку на событие.
* Сохраняющиеся события почти всегда медленнее, чем в операциях памяти, однако разница в скорости сильно зависит от характеристик диска. Компромисс между скоростью и надежностью присущ всем системам обмена сообщениями, но, как правило, становится заметным только в больших масштабах.

Для обеспечения настойчивости событий в `persistencePolicy` `true`подписке на событие, установите:

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
