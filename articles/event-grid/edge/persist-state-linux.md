---
title: Сохранение состояния в службе "Сетка событий Azure" IoT Edge | Документация Майкрософт
description: Сохранение метаданных в Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 39b16c6cfd5b94d412827ed88197edbef2da1453
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844638"
---
# <a name="persist-state-in-linux"></a>Сохранение состояния в Linux

Разделы и подписки, созданные в модуле сетки событий, по умолчанию хранятся в файловой системе контейнера. Без сохранения при повторном развертывании модуля все созданные метаданные будут потеряны. Чтобы сохранить данные между развертываниями и перезапусками, необходимо сохранить данные вне файловой системы контейнера.

По умолчанию сохраняются только метаданные, и события по-прежнему хранятся в памяти для повышения производительности. Чтобы включить сохраняемость событий, следуйте разделу материализованные события.

В этой статье описаны действия по развертыванию модуля службы "Сетка событий" с сохранением в развертываниях Linux.

> [!NOTE]
>Модуль "Сетка событий" работает как пользователь с низким уровнем привилегий с идентификатором UID `2000` и именем `eventgriduser`.

## <a name="persistence-via-volume-mount"></a>Сохраняемость через подключение тома

 [Тома DOCKER](https://docs.docker.com/storage/volumes/) используются для сохранения данных в разных развертываниях. Вы можете разрешить DOCKER автоматически создать именованный том как часть развертывания модуля сетки событий. Этот параметр является простейшим. Имя тома, создаваемого в разделе **BIND** , можно указать следующим образом.

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
>Не изменяйте вторую часть значения привязки. Он указывает на определенное расположение внутри модуля. Для модуля "Сетка событий" в Linux он должен быть **/АПП/метадатадб**.

Например, следующая конфигурация приведет к созданию тома **егметадатадбвол** , в котором будут храниться метаданные.

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
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

Вместо подключения тома можно создать каталог в главной системе и подключить этот каталог.

## <a name="persistence-via-host-directory-mount"></a>Сохранение с помощью подключения к каталогу узлов

Вместо тома DOCKER можно также подключить папку узла.

1. Сначала создайте пользователя с именем **евентгридусер** и идентификатором **2000** на главном компьютере, выполнив следующую команду:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Создайте каталог в файловой системе узла, выполнив следующую команду.

   ```sh
   md <your-directory-name-here>
   ```

    Например, при выполнении следующей команды будет создан каталог с именем **михостдир**.

    ```sh
    md /myhostdir
    ```
1. Затем сделайте **евентгридусер** владельцем этой папки, выполнив следующую команду.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Например,

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Используйте **привязки** для подключения каталога и повторного развертывания модуля службы "Сетка событий" из портал Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

    Например,

    ```json
    {
          "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
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
    >Не изменяйте вторую часть значения привязки. Он указывает на определенное расположение внутри модуля. Для модуля "Сетка событий" в Linux он должен быть **/АПП/Метадата**.


## <a name="persist-events"></a>Сохранить события

Чтобы включить сохраняемость событий, необходимо сначала включить сохранение метаданных с помощью подключения тома или подключения к каталогу узла, используя приведенные выше разделы.

Важные моменты, которые следует учитывать при сохранении событий:

* Сохранение событий включено для каждой подписки на события и является явной присоединением тома или каталога.
* Сохраняемость событий настраивается в подписке на событие во время создания и не может быть изменена после создания подписки на события. Чтобы переключить сохраняемость событий, необходимо удалить и повторно создать подписку на события.
* Сохранение событий почти всегда выполняется медленнее, чем в операциях с памятью, однако разница в скорости сильно зависит от характеристик диска. Компромисс между скоростью и надежностью относится ко всем системам обмена сообщениями, но обычно становится нотиЦибле в крупном масштабе.

Чтобы включить сохраняемость событий в подписке на события, задайте для `persistencePolicy` значение `true`.

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