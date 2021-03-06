---
title: Управление пользователями и ролями в IoT Central приложении Azure | Документация Майкрософт
description: Как администратор, как управлять пользователями и ролями в приложении IoT Central Azure;
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 9729a51c36a520a2c196fb83515c9fa616411cf3
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974433"
---
# <a name="manage-users-and-roles-in-your-iot-central-application-preview-features"></a>Управление пользователями и ролями в приложении IoT Central (Предварительная версия компонентов)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

В этой статье описывается, как администратор может добавлять, изменять и удалять пользователей в приложении IoT Central Azure. В статье также описывается, как управлять ролями в приложении IoT Central Azure.

Чтобы получить доступ к разделу **Администрирование**, вам должна быть назначена роль **Администратор** приложения Azure IoT Central. При создании приложения IoT Central Azure автоматически добавляется к роли **администратора** этого приложения.

## <a name="add-users"></a>Добавление пользователей

Каждый пользователь должен иметь учетную запись, чтобы войти в систему и получить доступ к приложению Azure IoT Central. Учетные записи MSA и Azure Active Directory поддерживаются в Azure IoT Central. Группы Azure Active Directory сейчас не поддерживаются в Azure IoT Central.

Дополнительные сведения см. на странице [справки по учетной записи Майкрософт](https://support.microsoft.com/products/microsoft-account?category=manage-account) и в статье [Краткое руководство по добавлению новых пользователей в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Чтобы добавить пользователя в приложение IoT Central, в разделе **Администрирование** перейдите на страницу **Пользователи**.
    
    > [!div class="mx-imgBorder"]
    >![Управление пользователями](media/howto-manage-users-roles/manage-users-pnp.png)

1. На странице **Пользователи** выберите **+Добавить пользователя**.

1. Выберите роль в раскрывающемся списке **Роль**. Дополнительные сведения о ролях см. в разделе [Управление ролями](#manage-roles) этой статьи.

    > [!div class="mx-imgBorder"]
    >![добавить пользователя и выбрать роль](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Пользователь, который находится в пользовательской роли, предоставляющей им разрешение на добавление других пользователей, может добавлять пользователей только к роли с тем же или меньшим числом разрешений, чем их собственная роль.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Изменение ролей, назначенных пользователям

Роли нельзя изменить после назначения. Чтобы изменить роль, назначенную пользователю, удалите учетную запись и повторно добавьте пользователя с другой ролью.

> [!NOTE]
> Назначенные роли относятся только к IoT Centralному приложению и не могут управляться с помощью портала Azure.

## <a name="delete-users"></a>Удаление пользователей

Чтобы удалить пользователей, установите один или несколько флажков на странице **Пользователи**. Теперь щелкните **Удалить**.

## <a name="manage-roles"></a>управлять ролями;

Роли позволяют контролировать, кто в вашей организации может выполнять различные задачи в IoT Central. Существует три встроенных роли, которые можно назначить пользователям приложения. Кроме того, можно [создавать пользовательские роли](#create-a-custom-role) , если требуется более детализированный контроль.

> [!div class="mx-imgBorder"]
> ![управление ролями](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Администратор

Пользователи с ролью **администратора** могут управлять и контролировать каждую часть приложения, включая выставление счетов.

Пользователю, создавшему приложение, автоматически назначается роль **администратора**. В роли **администратора** должен быть хотя бы один пользователь.

### <a name="builder"></a>Конструктор

Пользователи в роли **построителя** могут управлять каждой частью приложения, но не могут вносить изменения на вкладках администрирование или непрерывное экспорт данных.

### <a name="operator"></a>operator

Пользователи в роли **оператора** могут отслеживать работоспособность и состояние устройства. Они не могут вносить изменения в шаблоны устройств или администрировать приложение. Операторы могут добавлять и удалять устройства, управлять наборами устройств и выполнять аналитику и задания. 

## <a name="create-a-custom-role"></a>Создание настраиваемой роли

Если для решения требуются детализированные элементы управления доступом, можно создать пользовательские роли с пользовательскими наборами разрешений. Чтобы создать пользовательскую роль, перейдите на страницу **роли** в разделе **Администрирование** приложения. Затем выберите **+ создать роль**и добавьте имя и описание для своей роли. Выберите разрешения, которые требуются для роли, и нажмите кнопку **сохранить**.

Пользователи могут добавлять пользователей к пользовательской роли так же, как и к встроенной роли.

> [!div class="mx-imgBorder"]
> ![создать настраиваемую роль](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Параметры настраиваемой роли

При определении пользовательской роли выбирается набор разрешений, предоставленных пользователю, если он является членом роли. Некоторые разрешения зависят от других. Например, при добавлении к роли разрешения на **Обновление панелей мониторинга** приложений автоматически добавляется разрешение **Просмотр панелей мониторинга приложений** . В следующих таблицах перечислены доступные разрешения и их зависимости, которые можно использовать при создании пользовательских ролей.

#### <a name="managing-devices"></a>Управление устройствами

**Разрешения для шаблона устройства**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет     |
| управление | Показать <br/> Другие зависимости: просмотр экземпляров устройств  |
| Полный доступ | Просмотр, управление <br/> Другие зависимости: просмотр экземпляров устройств |

**Разрешения для экземпляра устройства**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет <br/> Другие зависимости: Просмотр шаблонов устройств и групп устройств |
| Обновить | Показать <br/> Другие зависимости: Просмотр шаблонов устройств и групп устройств  |
| Create | Показать <br/> Другие зависимости: Просмотр шаблонов устройств и групп устройств  |
| Удаление | Показать <br/> Другие зависимости: Просмотр шаблонов устройств и групп устройств  |
| Выполнение команд | Обновление, просмотр <br/> Другие зависимости: Просмотр шаблонов устройств и групп устройств  |
| Полный доступ | Просмотр, обновление, создание, удаление, выполнение команд <br/> Другие зависимости: Просмотр шаблонов устройств и групп устройств  |

**Разрешения для групп устройств**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет <br/> Другие зависимости: Просмотр шаблонов устройств и экземпляров устройств |
| Обновить | Показать <br/> Другие зависимости: Просмотр шаблонов устройств и экземпляров устройств   |
| Create | Просмотр, обновление <br/> Другие зависимости: Просмотр шаблонов устройств и экземпляров устройств   |
| Удаление | Показать <br/> Другие зависимости: Просмотр шаблонов устройств и экземпляров устройств   |
| Полный доступ | Просмотр, обновление, создание, удаление <br/> Другие зависимости: Просмотр шаблонов устройств и экземпляров устройств |

**Разрешения на управление подключением устройств**

| Name | Зависимости |
| ---- | -------- |
| Чтение экземпляра | Нет <br/> Другие зависимости: Просмотр шаблонов устройств, групп устройств, экземпляров устройств |
| Управление экземпляра | Нет |
| Чтение глобального | Нет   |
| Управление глобальными | Чтение глобального |
| Полный доступ | Чтение экземпляра, управление экземплярами, чтение глобальных, Управление глобальными. <br/> Другие зависимости: Просмотр шаблонов устройств, групп устройств, экземпляров устройств |

**Разрешения заданий**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет <br/> Другие зависимости: Просмотр шаблонов устройств, экземпляров устройств и групп устройств |
| Обновить | Показать <br/> Другие зависимости: Просмотр шаблонов устройств, экземпляров устройств и групп устройств |
| Create | Просмотр, обновление <br/> Другие зависимости: Просмотр шаблонов устройств, экземпляров устройств и групп устройств |
| Удаление | Показать <br/> Другие зависимости: Просмотр шаблонов устройств, экземпляров устройств и групп устройств |
| Выполнить | Показать <br/> Другие зависимости: Просмотр шаблонов устройств, экземпляров устройств и групп устройств; Обновление экземпляров устройств; Выполнять команды в экземплярах устройств |
| Полный доступ | Просмотр, обновление, создание, удаление, выполнение <br/> Другие зависимости: Просмотр шаблонов устройств, экземпляров устройств и групп устройств; Обновление экземпляров устройств; Выполнять команды в экземплярах устройств |

**Разрешения правил**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет <br/> Другие зависимости: Просмотр шаблонов устройств |
| Обновить | Показать <br/> Другие зависимости: Просмотр шаблонов устройств |
| Create | Просмотр, обновление <br/> Другие зависимости: Просмотр шаблонов устройств |
| Удаление | Показать <br/> Другие зависимости: Просмотр шаблонов устройств |
| Полный доступ | Просмотр, обновление, создание, удаление <br/> Другие зависимости: Просмотр шаблонов устройств |

#### <a name="managing-the-app"></a>Управление приложением

**Разрешения для параметров приложения**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет     |
| Обновить | Показать   |
| Копировать | Показать <br/> Другие зависимости: Просмотр шаблонов устройств, экземпляров устройств, групп устройств, панелей мониторинга, экспорта данных, фирменной символики, ссылок на справку, пользовательских ролей, правил |
| Удаление | Показать   |
| Полный доступ | Просмотр, обновление, копирование, удаление <br/> Другие зависимости: Просмотр шаблонов устройств, групп устройств, панелей мониторинга приложений, экспорт данных, фирменная символика, ссылки справки, пользовательские роли, правила |

**Разрешения на экспорт шаблона приложения**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет     |
| Экспортировать | Показать <br/> Другие зависимости: Просмотр шаблонов устройств, экземпляров устройств, групп устройств, панелей мониторинга, экспорта данных, фирменной символики, ссылок на справку, пользовательских ролей, правил |
| Полный доступ | Просмотр, экспорт <br/> Другие зависимости: Просмотр шаблонов устройств, групп устройств, панелей мониторинга приложений, экспорт данных, фирменная символика, ссылки справки, пользовательские роли, правила |

**Разрешения на выставление счетов**

| Name | Зависимости |
| ---- | -------- |
| управление | Нет     |
| Полный доступ | управление |

#### <a name="managing-users-and-roles"></a>Управление пользователями и ролями

**Разрешения пользовательских ролей**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет |
| Обновить | Показать |
| Create | Просмотр, обновление |
| Удаление | Показать |
| Полный доступ | Просмотр, обновление, создание, удаление |

**Разрешения на управление пользователями**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет <br/> Другие зависимости: Просмотр пользовательских ролей |
| Добавить | Показать <br/> Другие зависимости: Просмотр пользовательских ролей |
| Удаление | Показать <br/> Другие зависимости: Просмотр пользовательских ролей |
| Полный доступ | Просмотр, добавление, удаление <br/> Другие зависимости: Просмотр пользовательских ролей |

> [!NOTE]
> Пользователь, который находится в пользовательской роли, предоставляющей им разрешение на добавление других пользователей, может добавлять пользователей только к роли с тем же или меньшим числом разрешений, чем их собственная роль.

#### <a name="customizing-the-app"></a>Настройка приложения

**Разрешения панели мониторинга приложения**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет     |
| Обновить | Показать   |
| Create | Просмотр, обновление |
| Удаление | Показать   |
| Полный доступ | Просмотр, обновление, создание, удаление |

**Разрешения личных панелей мониторинга**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет     |
| Обновить | Показать   |
| Create | Просмотр, обновление   |
| Удаление | Показать   |
| Полный доступ | Просмотр, обновление, создание, удаление |

**Разрешения на фирменную символику, фавикон и цвета**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет     |
| Обновить | Показать   |
| Полный доступ | Просмотр, обновление |

**Разрешения ссылок на справку**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет     |
| Обновить | Показать   |
| Полный доступ | Просмотр, обновление |

#### <a name="extending-the-app"></a>Расширение приложения

**Разрешения на экспорт данных**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет     |
| Обновить | Показать   |
| Create | Просмотр, обновление  |
| Удаление | Показать   |
| Полный доступ | Просмотр, обновление, создание, удаление |

**Разрешения токена API**

| Name | Зависимости |
| ---- | -------- |
| Показать | Нет     |
| Create | Показать   |
| Удаление | Показать   |
| Полный доступ | Просмотр, создание, удаление |

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как управлять пользователями и ролями в приложении IoT Central Azure, рекомендуем следующий шаг — узнать, как [управлять счетом](howto-view-bill.md).
