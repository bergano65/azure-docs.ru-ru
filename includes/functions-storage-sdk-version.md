---
title: включение файла
description: включение файла
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: f8733ef907b8f31ace7ea72f705ba1b37d1adece
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132386"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Версия пакета SDK для службы хранилища Azure в решении "Функции" 1.x

В решении "Функции" 1.x триггеры и привязки службы хранилища используют версию 7.2.1 пакета SDK для службы хранилища Azure (пакет NuGet [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)). Если указать другую версию пакета SDK для службы хранилища и использовать привязку к типу пакета SDK для службы хранилища в сигнатуре функции, то среда выполнения "Функции" может сообщить, что выполнить привязку к этому типу невозможно. Чтобы избежать этого, убедитесь, в вашем проекте указан пакет [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
