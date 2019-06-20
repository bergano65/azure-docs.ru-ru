---
title: включение файла
description: включение файла
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185812"
---
ИМЯ | Коммерческий URL-адрес | Государственный URL-адрес | ОПИСАНИЕ
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Используется для управления доступом и удостоверениями с помощью Azure Active Directory. 
Azure Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Используется для передачи данных репликации и координации.
Репликация | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Используется для операций управления репликацией и координации.
Хранилище | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Используется для доступа к учетной записи хранения, в которой хранятся реплицируемые данные.
Телеметрия (необязательно) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Используется для телеметрии.
Синхронизация времени | ``time.windows.com`` | ``time.nist.gov`` | Используется для проверки синхронизации времени системы с глобальным временем во всех развертываниях.


