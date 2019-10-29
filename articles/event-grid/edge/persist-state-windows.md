---
title: Сохранение состояния в Windows — служба "Сетка событий Azure" IoT Edge | Документация Майкрософт
description: Сохранение состояния в Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992266"
---
# <a name="persist-state-in-windows"></a>Сохранение состояния в Windows

Разделы и подписки, созданные в модуле сетки событий, по умолчанию хранятся в файловой системе контейнера. Без сохранения при повторном развертывании модуля все созданные метаданные будут потеряны. Чтобы сохранить данные в разных развертываниях, необходимо сохранить данные вне файловой системы контейнера. В настоящее время сохраняются только метаданные. События хранятся в памяти. Если модуль сетки событий повторно развернут или перезапущен, все недоставленные события будут потеряны.

В этой статье описаны шаги, необходимые для развертывания модуля службы "Сетка событий" с сохраняемостью в развертываниях Windows.

> [!NOTE]
>Модуль сетки событий выполняется как пользователь с низким уровнем привилегий **контаинерусер** в Windows.

## <a name="persistence-via-volume-mount"></a>Сохраняемость через подключение тома

[Тома DOCKER](https://docs.docker.com/storage/volumes/) используются для сохранения данных в разных развертываниях. Чтобы подключить том, необходимо создать его с помощью команд DOCKER, предоставить разрешения, чтобы контейнер мог читать, записывать в него, а затем развертывать модуль. Автоматическое создание тома с необходимыми разрешениями в Windows не предусмотрено. Его необходимо создать перед развертыванием.

1. Создайте том, выполнив следующую команду:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Например,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Получите каталог узлов, на который сопоставляется том, выполнив команду ниже.

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Например,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Пример выходных данных:-

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
1. Добавьте группу " **Пользователи** " в значение, на которое указывает **точка** подключения, следующим образом:
    1. Запустите проводник.
    1. Перейдите в папку, на которую указывает **точка**подключения.
    1. Щелкните правой кнопкой мыши и выберите пункт **Свойства**.
    1. Выберите **Безопасность**.
    1. В разделе * имена групп или пользователей выберите **изменить**.
    1. Нажмите кнопку **Добавить**, введите `Users`, выберите **Проверить имена**и нажмите кнопку **ОК**.
    1. В разделе *разрешения для пользователей*выберите **изменить**и нажмите кнопку **ОК**.
1. Используйте **привязки** для подключения этого тома и повторного развертывания модуля сетки событий из портал Azure

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
   >Не изменяйте вторую часть значения привязки. Он указывает на определенное расположение в модуле. Для модуля сетки событий в Windows он должен иметь значение **C:\\app\\метадатадб**.


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
                "myeventgridvol:C:\\app\\metadataDb"
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

## <a name="persistence-via-host-directory-mount"></a>Сохранение с помощью подключения к каталогу узлов

Кроме того, можно выбрать создание каталога в главной системе и подключение этого каталога.

1. Создайте каталог в файловой системе узла, выполнив следующую команду.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Например,

   ```sh
   mkdir C:\myhostdir
   ```
1. Используйте **привязки** для подключения каталога и повторного развертывания модуля службы "Сетка событий" из портал Azure.

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
    >Не изменяйте вторую часть значения привязки. Он указывает на определенное расположение в модуле. Для модуля "Сетка событий" в Windows он должен иметь значение **C:\\app\\метадатадб**.

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
                "C:\\myhostdir:C:\\app\\metadataDb"
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
