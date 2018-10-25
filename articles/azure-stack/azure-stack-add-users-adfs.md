---
title: Добавление пользователей для работы с ADFS в Azure Stack | Документация Майкрософт
description: Сведения о добавлении пользователей для работы с развертываниями Azure Stack с помощью служб федерации Active Directory
services: azure-stack
documentationcenter: ''
author: patricka
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: patricka
ms.reviewer: unknown
ms.openlocfilehash: f8abacbcb05d1346931b5c2e1097660cfbd8e594
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344172"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Добавление пользователей Azure Stack в AD FS
Оснастку **Пользователи и компьютеры Active Directory** можно использовать для добавления дополнительных пользователей в среду Azure Stack, используя AD FS в качестве поставщика удостоверений.

## <a name="add-windows-server-active-directory-users"></a>Добавление пользователей Windows Server Active Directory
> [!TIP]
> В этом примере используется пакет по умолчанию azurestack.local ASDK для Active Directory. 

1.  Войдите в компьютер с учетной записью, предоставляющей доступ к инструментам администрирования Windows, и откройте новую консоль управления Microsoft (MMC).
2.  Щелкните **Файл > Добавить или удалить оснастку**.
3.  Выберите **Active Directory — пользователи и компьютеры** > **AzureStack.local** > **Пользователи**.
4.  Щелкните **Действия** > **Создать** > **Пользователь**.
5.  В окне "Новый объект — пользователь", укажите и подтвердите пароль.
6.  Нажмите кнопку **Далее**, чтобы подтвердить значения, и кнопку "Готово", чтобы создать пользователя.


## <a name="next-steps"></a>Дополнительная информация
[Создание субъектов-служб](azure-stack-create-service-principals.md)