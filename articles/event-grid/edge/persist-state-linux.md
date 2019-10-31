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
ms.openlocfilehash: 3506399537fe2cb16014ceb3429bce5aeee8cb69
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100333"
---
# <a name="persist-state-in-linux"></a>Сохранение состояния в Linux

Разделы и подписки, созданные в модуле сетки событий, по умолчанию хранятся в файловой системе контейнера. Без сохранения при повторном развертывании модуля все созданные метаданные будут потеряны. В настоящее время сохраняются только метаданные. События хранятся в памяти. Если модуль сетки событий повторно развернут или перезапущен, все недоставленные события будут потеряны.

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
      "egmetadataDbVol:/app/metadataDb"
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

Кроме того, можно создать том DOCKER с помощью команд клиента DOCKER. 

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
                  "/myhostdir:/app/metadataDb"
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
