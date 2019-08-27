---
title: включение файла
description: включение файла
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70049044"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

В этой статье вы создадите серверную службу, которая планирует задание для вызова прямого метода на устройстве, планирует задание для обновления двойникаа устройства и отслеживает ход выполнения каждого задания. Для выполнения этих операций службе требуются разрешения на **Чтение реестра** и **запись в реестр** . По умолчанию каждый центр Интернета вещей создается с помощью политики общего доступа с именем **registryReadWrite** , которая предоставляет эти разрешения.
