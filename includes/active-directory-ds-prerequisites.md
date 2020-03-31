---
title: включить файл
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68426947"
---
> [!IMPORTANT]
> **Прежде чем выполнять инструкции в этой статье, включите синхронизацию хэша паролей в доменных службах Azure AD.**
>
> Следуйте инструкциям, приведенным ниже, в зависимости от типа пользователей в вашем каталоге Azure AD. Выполните оба набора инструкций, если в вашем каталоге Azure AD используется сочетание облачных и синхронизируемых учетных записей пользователей. Вы не можете быть в состоянии выполнить следующие операции в случае, если вы пытаетесь использовать учетную запись B2B Гость (например, ваш gmail или MSA от другого поставщика identity, который мы позволяем), потому что у нас нет пароля для этих пользователей синхронизированы с управляемым доменом, как это учетные записи гостей в каталоге. Полная информация об этих учетных записях, включая их пароли, будет находиться за пределами Azure AD, а поскольку эта информация не находится в Azure AD, следовательно, она даже не синхронизируется с управляемым доменом. 
> - [Инструкции для облачных учетных записей пользователей](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Инструкции для учетных записей пользователей, синхронизируемых из локального каталога](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
