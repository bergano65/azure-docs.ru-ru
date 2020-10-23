---
title: Проверка подлинности SSH с помощью Azure Active Directory
description: Руководство по архитектуре по обеспечению интеграции SSH с Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb766150339820f9356fe94311cd1ff33dda5480
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462977"
---
# <a name="ssh"></a>SSH  

Secure Shell (SSH) — это сетевой протокол, обеспечивающий безопасное шифрование служб операционной сети в незащищенной сети. SSH также предоставляет вход в командной строке, выполняет удаленные команды и безопасно передает файлы. Обычно он используется в системах на базе UNIX, таких как Linux®. SSH заменяет протокол Telnet, который не обеспечивает шифрование в незащищенной сети. 

Azure Active Directory (Azure AD) предоставляет расширение виртуальной машины для систем на основе® Linux, работающих в Azure. 

## <a name="use-when"></a>Используется, если 

* Работа с виртуальными машинами на базе Linux®, для которых требуется удаленный вход

* Исполнение удаленных команд в системах на базе Linux®

* Безопасная Перенаправление файлов в незащищенной сети

![Схема Azure AD с протоколом SSH](./media/authentication-patterns/ssh-auth.png)

SSH с Azure AD

## <a name="components-of-system"></a>Компоненты системы 

* **Пользователь**: запускает SSH-клиент для установки подключения к виртуальным машинам Linux® и предоставляет учетные данные для проверки подлинности.

* **Веб-браузер**: компонент, с которым взаимодействует пользователь. Он взаимодействует с поставщиком удостоверений (Azure AD) для безопасной проверки подлинности и авторизации пользователя.

* **Клиент SSH**. управляет процессом установки подключения.

* **Azure AD**: проверяет подлинность удостоверения пользователя с помощью потока устройства и маркера проблем для виртуальных машин Linux.

* **Виртуальная машина Linux**: принимает токен и обеспечивает успешное подключение.

## <a name="implement-ssh-with-azure-ad"></a>Реализация SSH с помощью Azure AD 

* [Вход в виртуальную машину Linux® с учетными данными Azure Active Directory — виртуальные машины Azure ](https://docs.microsoft.com/azure/virtual-machines/linux/login-using-aad) 

* [Поток кода устройства OAuth 2,0 — платформа Microsoft Identity ](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)

* [Интеграция с Azure Active Directory (akamai.com)](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

 
