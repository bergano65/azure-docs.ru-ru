---
title: Устойчивое состояние в Windows - Azure Event Grid IoT Edge Документы Майкрософт
description: Устойчивое состояние в Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c2bae3bd268dba8efdf23ae314671b17a2c89420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086627"
---
# <a name="persist-state-in-windows"></a>Устойчивое состояние в Windows

Темы и подписки, созданные в модуле Event Grid, хранятся в файловой системе контейнеров по умолчанию. Без настойчивости, если модуль будет передислоцирован, все созданные метаданные будут потеряны. Чтобы сохранить данные при развертывании и перезагрузке, необходимо сохранять данные за пределами файловой системы контейнеров. 

По умолчанию сохраняются только метаданные, а события по-прежнему хранятся в памяти для повышения производительности. Следуйте разделу упорствусобытий, чтобы также обеспечить сохранение событий.

В этой статье представлены шаги, необходимые для развертывания модуля Event Grid с сохранением в развертывании Windows.

> [!NOTE]
>Модуль Event Grid работает как низкопривилегированный пользователь **ContainerUser** в Windows.

## <a name="persistence-via-volume-mount"></a>Настойчивость через объемкрепления

[Объемы докеров](https://docs.docker.com/storage/volumes/) используются для сохранения данных в развертывании. Чтобы смонтировать громкость, необходимо создать его с помощью команд докеров, дать разрешения, чтобы контейнер мог читать, писать ему, а затем развертывать модуль.

1. Создайте громкость, запустив следующую команду:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Например,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Получите каталог хоста, который отображает объем карты, запустив нижеприведенную команду

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Например,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Выход образца:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Добавьте группу **пользователей** к значению, указанному **Mountpoint** следующим образом:
    1. Запуск файлов Explorer.
    1. Перейдите к папке, указанной **Mountpoint.**
    1. Нажмите правой кнопкой мыши, а затем выберите **Свойства**.
    1. Выберите **безопасность**.
    1. Под именами пользователей «Группа» или «Группа» выберите **Edit**.
    1. Выберите **Добавить,** введите, `Users`выберите **Проверить имена,** и выберите **Ok**.
    1. Под *разрешениями для пользователей*выберите **Modify**и выберите **Ok**.
1. Используйте **Привязки** для установки этого объема и передислокации модуля Event Grid с портала Azure

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >Не изменяйте вторую часть значения связывания. Он указывает на определенное место в модуле. Для модуля Event Grid на окнах он должен быть **C:\\приложение\\metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>Настойчивость через хост-каталогьного монтажа

Вместо установки тома можно создать каталог в системе хоста и смонтировать этот каталог.

1. Создайте каталог в файловой системе хоста, запустив следующую команду.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Например,

   ```sh
   mkdir C:\myhostdir
   ```
1. Используйте Binds для **установки** каталога и передислокации модуля Event Grid с портала Azure.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Не изменяйте вторую часть значения связывания. Он указывает на определенное место в модуле. Для модуля Event Grid на окнах он должен быть **C:\\приложение\\metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>Устойчивые события

Для обеспечения сохранения событий необходимо сначала включить сохранение событий либо с помощью крепления громкости, либо крепления каталога с использованием вышеуказанных разделов.

Важные вещи, которые следует отметить в отношении сохраняющихся событий:

* Упорные события включены на основе подписки событий и выбираются после установки тома или каталога.
* Сохранение событий настраивается на подписку событий во время создания и не может быть изменено после создания подписки на событие. Чтобы переключить настойчивость событий, необходимо удалить и воссоздать подписку на событие.
* Сохраняющиеся события почти всегда медленнее, чем в операциях памяти, однако разница в скорости сильно зависит от характеристик диска. Компромисс между скоростью и надежностью присущ всем системам обмена сообщениями, но только становится заметным в больших масштабах.

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