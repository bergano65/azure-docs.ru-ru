---
title: удаленный рабочий стол служб шлюза с Azure Active Directory
description: Рекомендации по архитектуре для достижения удаленный рабочий стол служб шлюза с Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57466cccb71444d5711a9221c324a107757c5e82
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576801"
---
# <a name="remote-desktop-gateway-services"></a>Службы шлюза удаленный рабочий стол

Развертывание Standard службы удаленных рабочих столов (RDS) включает различные [службы ролей удаленный рабочий стол](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) , работающие под Windows Server. В развертывании RDS с помощью прокси приложения Azure Active Directory (Azure AD) имеется постоянное исходящее подключение с сервера, на котором запущена служба соединителя. Другие развертывания оставляют открытыми входящие подключения через подсистему балансировки нагрузки. Этот шаблон проверки подлинности позволяет предлагать больше типов приложений, публикуя локальные приложения с помощью службы удаленных рабочих столов. Это также сокращает область атаки развертывания с помощью AD Application Proxy Azure.

## <a name="use-when"></a>Используется, если

Необходимо предоставить удаленный доступ и защитить развертывание службы удаленных рабочих столов с предварительной проверкой подлинности.

![Схема архитектуры](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>Компоненты системы

* **Пользователь** : доступ к RDS, обслуживаемой прокси приложения.

* **Веб-браузер**. компонент, с которым взаимодействует пользователь, чтобы получить доступ к внешнему URL-адресу приложения.

* **Azure AD** : проверяет подлинность пользователя. 

* **Служба прокси приложения**. действует как обратный прокси-сервер для пересылки запроса от пользователя к RDS. Прокси приложения также может применять любые политики условного доступа. 

* **Службы удаленных рабочих столов**. действует как платформа для отдельных виртуализированных приложений, обеспечивая безопасный доступ к мобильным и удаленным рабочим столам, а также предоставление конечным пользователям возможности запуска приложений и настольных систем из облака. 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>Реализация служб шлюза удаленный рабочий стол с помощью Azure AD

* [Публикация удаленного рабочего стола с помощью Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services) 

* [Добавление локального приложения для удаленного доступа через прокси приложения в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
