---
title: Установка агента подготовки облака Azure AD Connect
description: В этой статье описывается, как установить облачный агент Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263352"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Установка агента подготовки облака Azure AD Connect
Этот документ проветривает процесс установки для агента подключения Azure Active Directory (Azure AD) и способы первоначальной настройки на портале Azure.

>[!IMPORTANT]
>Следующие инструкции по установке предполагают, что все предпосылки были [выполнены.](how-to-prerequisites.md)

Установка и настройка подготовки Azure AD Connect выполняется следующими шагами:
    
- [Установка агента.](#install-the-agent)
- [Проверка установки агента](#verify-agent-installation)


## <a name="install-the-agent"></a>Установка агента.
Чтобы установить агент, выполните следующие действия.

1. Войти на сервер, который вы будете использовать с разрешениями корпоративного администратора.
1. Перейдите на портал Azure. Слева выберите **Активный каталог Azure.**
1. Выберите **Управление подготовкой (предварительный просмотр)** > **Просмотреть все агенты**.
1. Загрузите агент по подготовке Azure AD Connect с портала Azure.

   ![Скачать агент по предварительному интернету](media/how-to-install/install9.png)</br>
1. Выполнить установку подключения Azure AD Connect (AADConnectProvisioning.Installer).
1. На экране пакета microsoft **Azure AD Connect Connect,** принять условия лицензирования и выбрать **установку.**

   ![Экран пакета услуг Microsoft Azure AD Connect](media/how-to-install/install1.png)</br>

1. После завершения операции запускается мастер конфигурации. Войдите с учетной записью глобального администратора Azure AD.
1. На экране **Connect Active Directory** выберите **Добавить каталог**. Затем вопийте с учетной записью администратора Active Directory. Эта операция добавляет ваш каталог. Нажмите кнопку **Далее**.

   ![Подключение экрана активного каталога](media/how-to-install/install3.png)</br>

1. На **полноэкранном** экране конфигурации выберите **Confirm.** Эта операция регистрирует и перезапускает агент.

   ![Экран завершения настройки](media/how-to-install/install4.png)</br>

1. После завершения операции следует увидеть уведомление о **успешной проверке конфигурации агента.** Выберите **выход**.

   ![кнопка выхода;](media/how-to-install/install5.png)</br>
1. Если вы все еще видите начальный экран пакета подключения **Microsoft Azure AD,** выберите **Close.**

## <a name="verify-agent-installation"></a>Проверка установки агента
Проверка агента происходит на портале Azure и на локальном сервере, который управляет агентом.

### <a name="azure-portal-agent-verification"></a>Проверка агента на портале Azure
Чтобы проверить агента, просматриваемый Azure, выполните следующие действия.

1. Войдите на портал Azure.
1. Слева выберите **Azure Active Directory** > **Azure AD Connect.** В центре выберите **Управление подготовкой (предварительный просмотр).**

   ![Портал Azure](media/how-to-install/install6.png)</br>

1.  На экране **обеспечения (предварительного просмотра) Azure AD** выберите **Обзор всех агентов.**

    ![Просмотрите все опции агентов](media/how-to-install/install7.png)</br>
 
1. На экране **on-premises, подающих агенты,** вы видите установленных агентов. Убедитесь, что нужный агент присутствует в этом списке с пометкой *active* (Активный).

   ![Экран агентов по предварительному обеспечению](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Проверить порт
Чтобы убедиться, что Azure слушает в порту 443 и что ваш агент может общаться с ним, выполните следующие действия.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Этот тест проверяет, что агенты могут общаться с Azure через порт 443. Откройте браузер и перейдите на предыдущий URL с сервера, на котором установлен агент.

![Проверка доступности порта](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>На локальном сервере
Чтобы убедиться, что агент работает, выполните следующие действия.

1.  Войти на сервер с учетной записью администратора.
1.  Открытые **службы,** либо перенаваясь на него, либо отправляясь в **Start** > **Run** > **Services.msc.**
1.  В **рамках служб**убедитесь, что Microsoft **Azure AD Connect Connect-Агента Updater** и **Microsoft Azure AD Connect Представитель Представитель Службы** Информации Агент есть, и их статус *работает.*

    ![Экран служб](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Агент был установлен, но он должен быть настроен и включен, прежде чем он начнет синхронизировать пользователей. Для настройки нового агента см. [Создать новую конфигурацию для облачной подготовки Azure AD Connect.](how-to-configure.md)



## <a name="next-steps"></a>Дальнейшие действия 

- [Что собой представляет подготовка?](what-is-provisioning.md)
- [Что такое подготовка облака Azure AD Connect?](what-is-cloud-provisioning.md)
 
