---
title: Управление доступом к ресурсам Azure с помощью управления привилегированными пользователями (PIM) в Azure AD
description: Дополнительные сведения об управлении доступом к ресурсам Azure с помощью управления привилегированными пользователями (PIM) в Azure AD и управления доступом на основе ролей (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 757068034868744b408c9402b521a0e4c73950f7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60344620"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Управление доступом к ресурсам Azure с помощью управления привилегированными пользователями в Azure AD

Для защиты привилегированных учетных записей от вредоносных кибератак можно использовать технологию Azure Active Directory Privileged Identity Management (PIM), чтобы уменьшить продолжительность действия привилегий и повысить вашу осведомленность об их использовании с помощью отчетов и оповещений. В PIM это достигается путем предоставления пользователям привилегий только в определенное время (JIT) либо путем назначения привилегий на короткий период, по истечении которого привилегии автоматически отзываются. 

Теперь вы можете использовать PIM с функцией управления доступом на основе ролей (RBAC) Azure для управления, контроля и отслеживания доступа к ресурсам Azure. PIM может управлять членством встроенных и пользовательских ролей, что помогает выполнять следующие задачи: 

- включать JIT-доступ к ресурсам Azure по запросу;
- автоматически прекращать доступ к ресурсам для назначенных пользователей и групп;
- назначать временный доступ к ресурсам Azure для быстрых задач или дежурных расписаний;
- получать оповещения, когда новым пользователям или группам назначается доступ к ресурсам и когда они активируют подходящие назначения.

Дополнительные сведения см. в статье [Что такое Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md)