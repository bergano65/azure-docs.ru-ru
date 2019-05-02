---
title: включение файла
description: включение файла
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 2b61cc8c5c448c28e96b06afa3556688a82567ed
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866810"
---
### <a name="delete-local-resources"></a>Удаление локальных ресурсов

Если вы хотите удалить среду выполнения IoT Edge и связанные ресурсы с устройства, используйте команды в соответствии с операционной системой устройства. 

#### <a name="windows"></a> Windows

Удалите среду выполнения IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

При удалении среды выполнения IoT Edge созданные с ее помощью контейнеры остановятся, но будут по-прежнему храниться на устройстве. Просмотрите все контейнеры.

   ```powershell
   docker ps -a
   ```

Удалите контейнеры среды выполнения, созданные на устройстве.

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Удалите дополнительные контейнеры, которые указаны в выходных данных `docker ps`, ссылаясь на имена контейнеров. 

#### <a name="linux"></a>Linux

Удалите среду выполнения IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

При удалении среды выполнения IoT Edge созданные с ее помощью контейнеры остановятся, но будут по-прежнему храниться на устройстве. Просмотрите все контейнеры.

   ```bash
   sudo docker ps -a
   ```

Удалите контейнеры среды выполнения, созданные на устройстве.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

Удалите дополнительные контейнеры, которые указаны в выходных данных `docker ps`, ссылаясь на имена контейнеров. 

Удалите среду выполнения контейнера.

   ```bash
   sudo apt-get remove --purge moby
   ```