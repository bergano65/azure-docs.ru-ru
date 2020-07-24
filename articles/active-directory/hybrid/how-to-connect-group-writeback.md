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
ms.openlocfilehash: 7992563bdfb318867e963ed4f371998bafbbbcdb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019919"
---
# <a name="azure-ad-connect-group-writeback"></a>Обратная запись группы Azure AD Connect

Обратная запись групп позволяет клиентам использовать облачные группы для их гибридных задач. Если вы используете функцию Группы Office 365, эти группы могут быть доступны в вашей локальной службе Active Directory. Этот параметр доступен **только** при наличии Exchange в локальной Active Directory.

## <a name="pre-requisites"></a>Предварительные требования
Для включения обратной записи групп должны быть выполнены следующие предварительные требования.
- Azure Active Directory Premium лицензий для вашего клиента.
- Настроенное гибридное развертывание между локальной организацией Exchange и Office 365 и проверено, что оно работает правильно.
- Установлена поддерживаемая версия Exchange в локальной среде
- Настроен единый вход с помощью Azure Active Directory Connect 

## <a name="enable-group-writeback"></a>Включение обратной записи групп
Чтобы включить обратную запись в группу, выполните следующие действия.

1. Откройте мастер Azure AD Connect, выберите **Настройка** , а затем нажмите кнопку **Далее**.
2. Выберите **настроить параметры синхронизации** , а затем нажмите кнопку **Далее**.
3. На странице **Подключение к Azure AD** введите свои учетные данные. Щелкните **Далее**.
4. На странице **Дополнительные компоненты** убедитесь, что настроенные ранее параметры по-прежнему выбраны.
5. Выберите элемент **обратная запись группы** , а затем нажмите кнопку **Далее**.
6. На **странице Обратная запись**выберите Active Directory подразделения (OU) для хранения объектов, которые синхронизированы из Office 365 в локальную организацию, а затем нажмите кнопку **Далее**.
7. На странице **все готово** для настройки нажмите кнопку **настроить**.
8. После завершения работы мастера нажмите кнопку **выход** на странице Настройка завершена.
9. Откройте Windows PowerShell на Azure Active Directory Connect сервере и выполните следующие команды.

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN
```

Дополнительные сведения о настройке групп Office 365 см. в статье [Настройка групп Microsoft 365 с помощью локального гибридного сервера Exchange](https://docs.microsoft.com/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect).

## <a name="disabling-group-writeback"></a>Отключение обратной записи группы
Чтобы отключить обратную запись группы, выполните следующие действия. 


1. Запустите мастер Azure Active Directory Connect и перейдите на страницу дополнительные задачи. Выберите задачу **Настройка параметров синхронизации** и нажмите кнопку **Далее**.
2. На странице **Дополнительные компоненты** снимите флажок обратная запись группы.  Вы получите предупреждение о том, что группы будут удалены.  Нажмите кнопку **Да**.
   >[!IMPORTANT]
   > Отключение обратной записи групп приведет к удалению всех групп, ранее созданных этой функцией, из локальной Active Directory при следующем цикле синхронизации. 

   ![Флажок снят](media/how-to-connect-group-writeback/group2.png)
  
3. Щелкните **Далее**.
4. Щелкните **Настройка**.

 >[!NOTE]
 > Отключение обратной записи групп установит флаги полного импорта и полной синхронизации в значение "true" на соединителе Azure Active Directory, что приведет к тому, что правило изменится для распространения в ходе следующего цикла синхронизации, удалив группы, которые ранее были записаны в Active Directory.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](whatis-hybrid-identity.md).
