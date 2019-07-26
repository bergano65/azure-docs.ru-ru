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
ms.openlocfilehash: 47d46cf4b400e29de8c526f750a10f57d6400220
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402659"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

В этой статье вы создадите серверную службу, которая планирует задание вызывать прямой метод на устройстве, планирует задание для обновления двойникаа устройства и отслеживает ход выполнения каждого задания. Для выполнения этих операций службе требуются разрешения на **Чтение реестра** и **запись в реестр** . По умолчанию каждый центр Интернета вещей создается с помощью политики общего доступа с именем **registryReadWrite** , которая предоставляет эти разрешения.
