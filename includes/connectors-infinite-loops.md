---
title: включение файла
description: включение файла
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 09/18/2019
ms.custom: include file
ms.openlocfilehash: 5647fed4e04743ca1dc9f90498d664ff3b8ac6ca
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121102"
---
> [!IMPORTANT]
> Будьте внимательны при обработке сообщений с помощью триггера и действия, которые имеют одинаковый тип соединителя и используют одну и ту же сущность обмена сообщениями, например очередь или подписку раздела. Это сочетание может создать бесконечный цикл, что приведет к созданию приложения логики, которое никогда не будет завершено.