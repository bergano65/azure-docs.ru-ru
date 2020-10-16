---
title: 'Azure AD Connect: Обратная запись группы'
description: В этой статье описывается обратная запись групп в Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2e24246c749978cd2bbb5b3d0821eea6d7dfb4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89660868"
---
# <a name="azure-ad-connect-group-writeback"></a>Обратная запись группы Azure AD Connect

Обратная запись групп позволяет клиентам использовать облачные группы для их гибридных задач. Если используется функция групп Microsoft 365, эти группы можно представить в локальном Active Directory. Этот параметр доступен **только** при наличии Exchange в локальной Active Directory.

## <a name="pre-requisites"></a>Предварительные требования
Для включения обратной записи групп должны быть выполнены следующие предварительные требования.
- Azure Active Directory Premium лицензий для вашего клиента.
- Настроенное гибридное развертывание между локальной организацией Exchange и Microsoft 365 и проверено, что оно работает правильно.
- Установлена поддерживаемая версия Exchange в локальной среде
- Настроен единый вход с помощью Azure Active Directory Connect 

## <a name="enable-group-writeback"></a>Включение обратной записи групп
Чтобы включить обратную запись в группу, выполните следующие действия.

1. Откройте мастер Azure AD Connect, выберите **Настройка** , а затем нажмите кнопку **Далее**.
2. Выберите **настроить параметры синхронизации** , а затем нажмите кнопку **Далее**.
3. На странице **Подключение к Azure AD** введите свои учетные данные. Щелкните **Далее**.
4. На странице **Дополнительные компоненты** убедитесь, что настроенные ранее параметры по-прежнему выбраны.
5. Выберите элемент **обратная запись группы** , а затем нажмите кнопку **Далее**.
6. На **странице Обратная запись**выберите Active Directory подразделения (OU) для хранения объектов, которые синхронизируются из Microsoft 365 с локальной организацией, а затем нажмите кнопку **Далее**.
7. На странице **все готово** для настройки нажмите кнопку **настроить**.
8. После завершения работы мастера нажмите кнопку **выход** на странице Настройка завершена.
9. Откройте Windows PowerShell от имени администратора на Azure Active Directory Connect сервере и выполните следующие команды.

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN
```

Дополнительные сведения о настройке групп Microsoft 365 см. в разделе [Настройка групп Microsoft 365 с использованием гибридной локальной среды Exchange](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect).

## <a name="disabling-group-writeback"></a>Отключение обратной записи группы
Чтобы отключить обратную запись группы, выполните следующие действия. 


1. Запустите мастер Azure Active Directory Connect и перейдите на страницу дополнительные задачи. Выберите задачу **Настройка параметров синхронизации** и нажмите кнопку **Далее**.
2. На странице **Дополнительные компоненты** снимите флажок обратная запись группы.  Вы получите предупреждение о том, что группы будут удалены.  Нажмите кнопку **Да**.
   >[!IMPORTANT]
   > Отключение обратной записи групп приведет к удалению всех групп, ранее созданных этой функцией, из локальной Active Directory при следующем цикле синхронизации. 

   ![Флажок снят](media/how-to-connect-group-writeback/group2.png)
  
3. Щелкните **Далее**.
4. Нажмите **Настроить**.

 >[!NOTE]
 > Отключение обратной записи групп установит флаги полного импорта и полной синхронизации в значение "true" на соединителе Azure Active Directory, что приведет к тому, что правило изменится для распространения в ходе следующего цикла синхронизации, удалив группы, которые ранее были записаны в Active Directory.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](whatis-hybrid-identity.md).