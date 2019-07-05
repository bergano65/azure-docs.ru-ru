---
title: Доменные службы Azure Active Directory. Обновление настроек DNS для виртуальной сети Azure | Документация Майкрософт
description: Приступая к работе с доменными службами Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: iainfou
ms.openlocfilehash: ef0c25227e98d066d75c9adb62af3f602e35db23
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474155"
---
# <a name="enable-azure-active-directory-domain-services"></a>Включение доменных служб Azure Active Directory

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Задача 4. Обновление настроек DNS для виртуальной сети Azure
В предыдущих задачах по настройке вы успешно включили доменные службы Azure Active Directory для своего каталога. Затем разрешите компьютерам в виртуальной сети подключаться к этим службам и использовать их. В этой статье мы обновим параметры DNS-сервера для виртуальной сети, указав два IP-адреса, по которым доменные службы Azure Active Directory доступны в виртуальной сети.

Чтобы обновить параметры DNS-сервера для виртуальной сети, в которой включены доменные службы Azure Active Directory, сделайте следующее:


1. На вкладке **Обзор** отображается набор **требуемых шагов конфигурации**, которые следует выполнить после полной подготовки управляемого домена. Первый шаг конфигурации — **обновление параметров DNS-сервера для виртуальной сети**.

    ![Доменные службы — вкладка "Обзор"](./media/getting-started/domain-services-provisioned-overview.png)

    > [!TIP]
    > Не отображается этот шаг настройки? Если параметры DNS-сервера для виртуальной сети актуальные, вы не увидите плитку "Обновление параметров сервера DNS для виртуальной сети" на вкладке "Обзор".
    >
    >

2. Щелкните **Настроить**, чтобы обновить параметры DNS-сервера для виртуальной сети.

> [!NOTE]
> Виртуальные машины в сети получат новые параметры DNS только после перезапуска. Чтобы получить обновленные параметры DNS сразу, активируйте перезапуск с помощью портала, PowerShell или CLI.
>
>

## <a name="next-step"></a>Дальнейшие действия
[Задача 5. Включение синхронизации хэшей паролей с доменными службами Azure AD](active-directory-ds-getting-started-password-sync.md)
