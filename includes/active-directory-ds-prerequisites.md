---
title: включение файла
description: Добавление файла с предварительными требованиями
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68426947"
---
> [!IMPORTANT]
> **Прежде чем выполнять инструкции в этой статье, включите синхронизацию хэша паролей в доменных службах Azure AD.**
>
> Следуйте инструкциям, приведенным ниже, в зависимости от типа пользователей в вашем каталоге Azure AD. Выполните оба набора инструкций, если в вашем каталоге Azure AD используется сочетание облачных и синхронизируемых учетных записей пользователей. Возможно, вы не сможете выполнить следующие операции на случай, если вы пытаетесь использовать гостевую учетную запись B2B (например, Gmail или MSA от другого поставщика удостоверений), так как у нас нет пароля для синхронизации этих пользователей с управляемым доменом. являются гостевыми учетными записями в каталоге. Полные сведения об этих учетных записях, включая пароли, будут находиться за пределами Azure AD, а эта информация не находится в Azure AD, поэтому она даже не синхронизируется с управляемым доменом. 
> - [Инструкции для облачных учетных записей пользователей](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Инструкции для учетных записей пользователей, синхронизируемых из локального каталога](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
