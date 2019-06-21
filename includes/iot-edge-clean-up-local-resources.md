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
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202695"
---
### <a name="delete-local-resources"></a>Удаление локальных ресурсов

Если вы хотите удалить среду выполнения IoT Edge и связанные ресурсы с устройства, используйте команды в соответствии с операционной системой устройства. 

#### <a name="windows"></a>Windows

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