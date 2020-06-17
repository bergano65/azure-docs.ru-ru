---
title: Установка агента подготовки облака Azure AD Connect
description: В этой статье описывается, как установить агент подготовки облака Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 764071eeaf368ecf78679632cffbd6964db40aa5
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681030"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Установка агента подготовки облака Azure AD Connect
В этом документе описывается процесс установки агента подготовки Azure Active Directory (Azure AD) Connect и его первоначальной настройки на портале Azure.

>[!IMPORTANT]
>В следующих инструкциях по установке предполагается, что выполнены все [необходимые условия](how-to-prerequisites.md).

Установка и настройка агента подготовки облака Azure AD Connect выполняется в следующем порядке.
    
- [Установка агента](#install-the-agent)
- [Проверка установки агента](#verify-agent-installation)


## <a name="install-the-agent"></a>Установка агента.
Для установки подключаемого модуля сделайте следующее.

1. Войдите на нужный сервер с правами администратора организации.
1. Перейдите на портал Azure. Слева выберите **Azure Active Directory**.
1. Выберите **Управление подготовкой (предварительная версия)**  > **Просмотр всех агентов**.
1. Скачайте агент подготовки Azure AD Connect на портале Azure.

   ![Скачивание локального агента](media/how-to-install/install9.png)</br>
1. Запустите установщик подготовки Azure AD Connect (AADConnectProvisioningAgent.Installer).
1. На экране **пакета агента подготовки Microsoft Azure AD Connect** примите условия лицензионного соглашения и щелкните **Установить**.

   ![Экран пакета агента подготовки Microsoft Azure AD Connect](media/how-to-install/install1.png)</br>

1. По завершении этой операции запустится мастер настройки. Войдите с учетной записью глобального администратора Azure AD.
1. На экране **Connect Active Directory** (Подключить Active Directory) щелкните **Добавить каталог**. Войдите в учетную запись администратора Active Directory. Эта операция добавляет локальный каталог. Выберите **Далее**.

   ![Экран подключения Active Directory](media/how-to-install/install3.png)</br>

1. На странице **Конфигурация завершена** щелкните **Подтвердить**. Эта операция регистрирует и перезапускает агент.

   ![Экран завершения настройки](media/how-to-install/install4.png)</br>

1. После завершения операции появится сообщение **Your agent configuration was successfully verified** (Конфигурация агента успешно проверена). Щелкните **Выход**.

   ![Кнопка "Выход"](media/how-to-install/install5.png)</br>
1. Если экран **пакета агента подготовки Microsoft Azure AD Connect** еще открыт, нажмите **Закрыть**.

## <a name="verify-agent-installation"></a>Проверка установки агента
Проверка агента выполняется на портале Azure и на локальном сервере, где выполняется агент.

### <a name="azure-portal-agent-verification"></a>Проверка агента на портале Azure
Чтобы убедиться, что агент доступен в Azure, сделайте следующее.

1. Войдите на портал Azure.
1. Слева щелкните **Azure Active Directory** > **Azure AD Connect**. Посередине выберите **Управление подготовкой (предварительная версия)** .

   ![Портал Azure](media/how-to-install/install6.png)</br>

1.  На экране **Подготовка Azure AD (предварительная версия)** щелкните **Проверить все агенты**.

    ![Действие проверки всех агентов](media/how-to-install/install7.png)</br>
 
1. На экране **локальных агентов подготовки** вы увидите установленные агенты. Убедитесь, что нужный агент присутствует в этом списке и помечен как *Активный*.

   ![Экран локальных агентов подготовки](media/how-to-install/verify1.png)</br>



### <a name="on-the-local-server"></a>На локальном сервере
Чтобы убедиться, что агент выполняется, сделайте следующее.

1.  Войдите на сервер, используя учетную запись администратора.
1.  Перейдите на страницу **Службы** или откройте ее, последовательно выбрав **Пуск** > **Выполнить** > **Services.msc**.
1.  В разделе **Службы** убедитесь, что **агент обновления Microsoft Azure AD Connect** и **агент подготовки Microsoft Azure AD Connect** присутствуют в списке и имеют состояние *Выполняется*.

    ![Экран служб](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>Итак, агент успешно установлен, но теперь его нужно настроить и включить, прежде чем он сможет синхронизировать пользователей. Сведения о настройке нового агента см. в статье [Создание новой конфигурации для облачной подготовки Azure AD Connect](how-to-configure.md).



## <a name="next-steps"></a>Дальнейшие действия 

- [Что собой представляет подготовка?](what-is-provisioning.md)
- [Что такое облачная подготовка Azure AD Connect?](what-is-cloud-provisioning.md)
 
