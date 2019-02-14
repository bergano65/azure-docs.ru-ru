---
title: Загрузка приложения прокси приложения занимает слишком много времени | Документы Майкрософт
description: Устранение проблем с производительностью загрузки страницы в прокси приложения Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4110ceddb55de1990d85597f8c1a9618743f946
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170437"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Загрузка приложения прокси приложения занимает слишком много времени

Эта статья поможет вам понять, почему загрузка приложения Azure AD Application Proxy может занимать долгое время. Здесь также объясняется, что можно сделать, чтобы устранить эту проблему.

## <a name="overview"></a>Обзор
Хотя ваши приложения работают, могут возникать большие задержки. Могут возникнуть модификации топологии сети, которые можно внести, чтобы повысить скорость. Оценку различных топологий см. в [документе с рекомендациями по сети](application-proxy-network-topology.md).

Помимо топологии сети в настоящее время не существует дополнительных рекомендаций по настройке производительности. По мере расширения службы прокси приложения она может оказаться ближе к физическому центру обработки данных. Более близкое расположение может помочь с задержкой. Список центров обработки данных Azure см. на [странице тестирования задержки](http://www.azurespeed.com/Azure/Latency). 

Центры обработки данных с прокси-службой приложения можно найти с помощью [средства проверки портов соединителя](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Отзывы о расположениях центров обработки данных для прокси приложения 
Могут существовать центры обработки данных Azure, которые пока не используют прокси приложения, но способны значительно сократить вашу задержку. Отправьте расположение центра обработки данных в aadapfeedback@microsoft.com. Корпорация Майкрософт использует отзывы в планах по расширению.

Корпорация Майкрософт работает над дополнительными возможностями снижения задержки. Документация будет обновлена, как только эти улучшения станут доступны.

## <a name="next-steps"></a>Дополнительная информация
[Работа с имеющимися локальными прокси-серверами](application-proxy-configure-connectors-with-proxy-servers.md)
