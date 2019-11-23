---
title: Как управлять устройствами с помощью портала Azure | Документация Майкрософт
description: Узнайте, как использовать портал Azure для управления устройствами.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c8e94a1b15ad8cd55019f9351c82f58130e472d
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420604"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Manage device identities using the Azure portal

With device identity management in Azure Active Directory (Azure AD), you can ensure that your users are accessing your resources from devices that meet your standards for security and compliance.

В этой статье:

- Assumes that you are familiar with the [introduction to device identity management in Azure Active Directory](overview.md)
- Provides you with information about managing your device identities using the Azure AD portal

## <a name="manage-device-identities"></a>Управление удостоверениями устройства

The Azure AD portal provides you with a central place to manage your device identities. Сюда можно перейти по [прямой ссылке](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) или с помощью следующих действий:

1. Войдите на [портал Azure](https://portal.azure.com) с учетной записью администратора.

2. Search for and select **Azure Active Directory** or select it from the **Home** page.

3. В разделе **Управление** щелкните **Устройства**.

   ![Настройка параметров устройства](./media/device-management-azure-portal/74.png)

На странице **Устройства** можно:

- Настройка параметров устройства
- Поиск устройств
- Perform device identity management tasks
- Review device-related audit logs  
  
## <a name="configure-device-settings"></a>Настройка параметров устройства

To manage your device identities using the Azure AD portal, your devices need to be either [registered or joined](overview.md) to Azure AD. Администратор может точно настроить процесс регистрации и присоединения устройств, задав параметры устройства.

![Настройка параметров устройства](./media/device-management-azure-portal/22.png)

На соответствующей странице устройства можно настроить следующие параметры:

![Управление устройством Intune](./media/device-management-azure-portal/21.png)

- **Users may join devices to Azure AD** - This setting enables you to select the users who can register their devices as Azure AD joined devices. Значение этого параметра по умолчанию — **Все**.

> [!NOTE]
> **Users may join devices to Azure AD** setting is only applicable to Azure AD join on Windows 10.

- **Дополнительные локальные администраторы на устройствах, присоединенных к Azure AD**. Этот параметр позволяет выбрать пользователей, которым предоставляются права локального администратора на устройстве. Добавленные здесь пользователи добавляются в роль *Администраторы устройств* в Azure AD. По умолчанию права локального администратора предоставляются глобальным администраторам в Azure AD и владельцам устройств. Этот параметр относится к возможностям выпуска Premium и доступен в таких продуктах, как Azure AD Premium или Enterprise Mobility Suite (EMS).
- **Users may register their devices with Azure AD** - You need to configure this setting to allow Windows 10 personal, iOS, Android, and macOs devices to be registered with Azure AD. If you select **None**, devices are not allowed to register with Azure AD. Для регистрации в Microsoft Intune или службе управления мобильными устройствами (MDM) для Office 365 требуется регистрация. Если вы настроили одну из этих служб, будет выбран пункт **Все**, а кнопка **Нет** будет отключена.
- **Require Multi-Factor Auth to join devices** - You can choose whether users are required to provide an additional authentication factor to join their device to Azure AD. По умолчанию используется значение **Нет**. Рекомендуется использовать Многофакторную идентификацию при регистрации устройства. Перед включением Многофакторной идентификации для этой службы необходимо убедиться, что Многофакторная идентификация настроена для пользователей, которые регистрируют свои устройства. Дополнительные сведения о различных службах Многофакторной идентификации в Azure см. в статье [Приступая к работе со службой "Многофакторная идентификация Microsoft Azure" в облаке](../authentication/concept-mfa-whichversion.md). 

> [!NOTE]
> **Require Multi-Factor Auth to join devices** setting applies to devices that are either Azure AD joined or Azure AD registered. This setting does not apply to hybrid Azure AD joined devices.

- **Maximum number of devices** - This setting enables you to select the maximum number of Azure AD joined or Azure AD registered devices that a user can have in Azure AD. По достижении этой квоты пользователь больше не сможет добавлять дополнительные устройства до тех пор, пока не будет удалены одно или несколько существующих устройств. По умолчанию используется значение **20**.

> [!NOTE]
> **Maximum number of devices** setting applies to devices that are either Azure AD joined or Azure AD registered. This setting does not apply to hybrid Azure AD joined devices.

- **Пользователи могут выполнять синхронизацию параметров и данных разных устройств**. По умолчанию этот параметр имеет значение **Нет**. Выбор конкретных пользователей, групп или значения "Все" позволяет синхронизировать параметры и данные приложений пользователя на его устройствах Windows 10. Узнайте больше о том, как работает синхронизация в Windows 10.
Этот параметр относится к возможностям Premium и доступен в таких продуктах, как Azure AD Premium или Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Поиск устройств

Есть два варианта обнаружения зарегистрированных и присоединенных к домену устройств:

- **Все устройства** в разделе **Управление** на странице **Устройства**;  

   ![Все устройства](./media/device-management-azure-portal/41.png)

- **Устройства** в разделе **Управление** на странице **Пользователь**.

   ![Все устройства](./media/device-management-azure-portal/43.png)

С помощью любого из этих средств можно получить представление, которое:

- Enables you to search for devices using the display name or device ID as filter.
- предоставляет подробный обзор зарегистрированных и присоединенных к домену устройств;
- позволяет выполнять общие задачи управления устройствами.

![Все устройства](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* If you see a device that is "Hybrid Azure AD joined" with a state "Pending" under the REGISTERED column, it indicates that the device has been synchronized from Azure AD connect and is waiting to complete registration from the client. Read more on how to [plan your Hybrid Azure AD join implementation](hybrid-azuread-join-plan.md). Additional information can be found in the article, [Devices frequently asked questions](faq.md).
>
>   ![Pending devices](./media/device-management-azure-portal/75.png)
>
>* Для некоторых устройств iOS, имена которых содержат апострофы, потенциально можно использовать другие знаки, которые выглядят как апострофы. So searching for such devices is a little tricky - if you are not seeing search results correctly, ensure that the search string contains matching apostrophe character.

## <a name="device-identity-management-tasks"></a>Device identity management tasks

As a global administrator or cloud device administrator, you can manage the registered or joined devices. Администраторы службы Intune могут:

- обновлять устройства — например, при ежедневных операциях, таких как включение и отключение устройств;
- удалять устройства — когда устройство списывается и должно быть удалено из Azure AD.

This section provides you with information about common device identity management tasks.

### <a name="manage-an-intune-device"></a>Управление устройством Intune

Если вы являетесь администратором Intune, то вы можете управлять устройствами, которые отмечены как **Microsoft Intune**. If the device is not enrolled with Microsoft Intune the "Manage" option will be greyed out.

![Управление устройством Intune](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Включение или отключение устройства Azure AD

Включить или отключить устройство можно с помощью:

- меню задач ("...") на странице **Все устройства**;

   ![Управление устройством Intune](./media/device-management-azure-portal/71.png)

- панели инструментов на странице **Устройства**.

   ![Управление устройством Intune](./media/device-management-azure-portal/32.png)

**Примечания:**

- You need to be a global administrator or cloud device administrator in Azure AD to enable / disable a device. 
- Disabling a device prevents a device from successfully authenticating with Azure AD, thereby preventing the device from accessing your Azure AD resources that are guarded by device CA or using your WH4B credentials.
- Disabling the device will revoke both the Primary Refresh Token (PRT) and any Refresh Tokens (RT) on the device.

### <a name="delete-an-azure-ad-device"></a>Удаление устройства Azure AD

Удалить отключить устройство можно с помощью:

- меню задач ("...") на странице **Все устройства**;

   ![Управление устройством Intune](./media/device-management-azure-portal/72.png)

- панели инструментов на странице **Устройства**.

   ![Удалить устройство.](./media/device-management-azure-portal/34.png)

**Примечания:**

- Для удаления устройства требуются права глобального администратора или администратора Intune в Azure AD.
- Удаление устройства:
   - оно больше не сможет обращаться к вашим ресурсам Azure AD;
   - будут удалены все данные, связанные с устройством, например ключи BitLocker для устройств Windows;  
   - вы не сможете отменить это действие (не рекомендуется выполнять без необходимости).

If a device is managed by another management authority (for example, Microsoft Intune), make sure that the device has been wiped / retired before deleting the device in Azure AD. Review how to [manage stale devices](device-management-azure-portal.md) before deleting any devices.

### <a name="view-or-copy-device-id"></a>Просмотр и копирование идентификатора устройства

С помощью идентификатора устройства можно проверять соответствующие сведения об устройстве или устранять неполадки, используя PowerShell. Чтобы перейти к функции копирования, щелкните устройство.

![Просмотр идентификатора устройства](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Просмотр и копирование ключей BitLocker

Вы можете просмотреть и скопировать ключи BitLocker, чтобы помочь пользователям восстановить свои зашифрованные диски. Эти ключи доступны только для устройств Windows, которые были зашифрованы и ключи которых были сохранены в Azure AD. Эти ключи можно скопировать, отобразив сведения об устройстве.

![Просмотр ключей BitLocker](./media/device-management-azure-portal/36.png)

Чтобы просмотреть или копировать ключи BitLocker, необходимо быть владельцем устройства или пользователем, которому назначена по крайней мере одна из следующих ролей:

- Администратор облачного устройства
- глобального администратора;
- Администратор службы технической поддержки
- Администратор службы Intune
- Администратор безопасности
- Читатель сведений о безопасности

> [!NOTE]
> Устройства Windows 10, присоединенные к гибридной среде Azure AD, не имеют владельца. Таким образом, если вы ищете устройство по владельцу и оно не обнаруживается, выполните поиск по идентификатору устройства.

## <a name="audit-logs"></a>Журналы аудита

Действия устройств можно просмотреть в журналах действий. These logs include activities triggered by the device registration service and by users:

- создание устройства и добавление владельцев или пользователей устройства;
- изменение параметров устройства;
- операции с устройством, например удаление или обновление устройства.

Анализ данных аудита следует начать с **журналов аудита** в разделе **Действие** на странице **Устройства**.

![Журналы аудита](./media/device-management-azure-portal/61.png)

Журнал аудита содержит представление списка по умолчанию, в котором отображаются:

- дата и время выполнения действия;
- целевые объекты;
- инициатор или субъект действия;
- действие.

![Журналы аудита](./media/device-management-azure-portal/63.png)

Представление списка можно настроить, щелкнув **Столбцы** на панели инструментов.

![Журналы аудита](./media/device-management-azure-portal/64.png)

Для сужения результатов до подходящего уровня вы можете отфильтровать данные аудита, используя следующие поля:

- Категория
- Тип ресурса действия
- Действие
- диапазон дат;
- Выбор пути миграции
- "Кем инициировано (субъект)".

Помимо применения фильтров можно выполнить поиск конкретных записей.

![Журналы аудита](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Дальнейшие действия

[How to manage stale devices in Azure AD](manage-stale-devices.md)
