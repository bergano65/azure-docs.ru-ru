---
title: Руководство. Интеграция существующего и нового лесов с одним клиентом Azure AD с помощью подготовки облака Azure AD Connect.
description: Руководство.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa14b45235f2a62f05e84efafc41b899dc531d49
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868683"
---
# <a name="integrate-an-existing-forest-and-a-new-forest-with-a-single-azure-ad-tenant"></a>Интеграция существующего и нового лесов с одним клиентом Azure AD

В этом руководстве описывается добавление подготовки облака в существующую среду гибридной идентификации. 

![Создание](media/tutorial-existing-forest/existing-forest-new-forest.png)

Среду, созданную в этом руководстве можно использовать для тестирования или дальнейшего знакомства с принципами работы гибридной идентификации. 

В этом сценарии есть существующий лес, синхронизированный с клиентом Azure AD с помощью Azure AD Connect. У вас есть новый лес, который нужно синхронизировать с одним и тем же клиентом Azure AD. Вы настроите подготовку облака для нового леса. 

## <a name="prerequisites"></a>Предварительные требования
### <a name="in-the-azure-active-directory-admin-center"></a>В центре администрирования Azure Active Directory

1. Создайте облачную учетную запись глобального администратора в клиенте Azure AD. Таким образом, вы сможете управлять конфигурацией клиента, если работа локальных служб завершится сбоем или они станут недоступными. Узнайте больше о [добавлении облачной учетной записи глобального администратора](../active-directory-users-create-azure-portal.md). Этот шаг очень важен, чтобы не потерять доступ к клиенту.
2. Добавьте одно [имя личного домена](../active-directory-domains-add-azure-portal.md) (или несколько) в клиент Azure AD. Пользователи могут выполнить вход с помощью одного из этих доменных имен.

### <a name="in-your-on-premises-environment"></a>В локальной среде

1. Идентификация присоединенного к домену сервера узла под управлением Windows Server 2012 R2 или более поздней версии с минимальным объемом ОЗУ 4 ГБ и .NET 4.7.1 + среда выполнения 

2. Если между серверами и Azure AD настроен брандмауэр, необходимо настроить указанные ниже элементы.
   - Убедитесь, что агенты могут передавать *исходящие* запросы в Azure AD через приведенные ниже порты.

     | Номер порта | Как он используется |
     | --- | --- |
     | **80** | Скачивание списков отзыва сертификатов при проверке SSL-сертификата. |
     | **443** | Обработка всего исходящего трафика для службы. |
     | **8080** (необязательно) | Агенты передают данные о своем состоянии каждые 10 минут через порт 8080, если порт 443 недоступен. Это данные о состоянии отображаются на портале Azure AD. |
     
     Если брандмауэр применяет правила в соответствии с отправляющими трафик пользователями, откройте эти порты для трафика, поступающего от служб Windows, которые работают как сетевая служба.
   - Если брандмауэр или прокси-сервер позволяет указать надежные суффиксы, можно добавить подключения к **\*.msappproxy.net** и **\*.servicebus.windows.net**. Если нет, разрешите доступ к [диапазонам IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653). Список диапазонов IP-адресов обновляется еженедельно.
   - Агентам требуется доступ к адресам **login.windows.net** и **login.microsoftonline.com** для первоначальной регистрации. Откройте эти URL-адреса в брандмауэре.
   - Для проверки сертификатов разблокируйте следующие URL-адреса: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** и **www\.microsoft.com:80**. Так как эти URL-адреса используются для проверки сертификатов в других продуктах Майкрософт, они уже могут быть разблокированы.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Установка агента подготовки Azure AD Connect
1. Войдите на сервер, присоединенный к домену.  Если вы используете учебник [по созданию базовой среды AD и Azure](tutorial-basic-ad-azure.md), это будет DC1.
2. Войдите на портал Azure, используя учетные данные глобального администратора облака.
3. Слева выберите **Azure Active Directory**, щелкните **Azure AD Connect** и в центре выберите **Управление подготовкой (предварительная версия)** .</br>
![портал Azure](media/how-to-install/install6.png)</br>
4. Щелкните "Скачивание агента".
5. Запуск агента подготовки Azure AD Connect
6. На экране-заставке **примите** условия лицензионного соглашения и щелкните **Установить**.</br>
![Экран приветствия](media/how-to-install/install1.png)</br>

7. После завершения этой операции откроется мастер настройки.  Войдите с учетной записью глобального администратора Azure AD.  Обратите внимание, что если он включен повышенный уровень безопасности IE, вход будет заблокирован.  В этом случае закройте окно установки, отключите повышенный уровень безопасности IE в диспетчере сервера и щелкните значок **мастера агента подготовки AAD Connect**, чтобы снова запустить установку.
8. На экране **Connect Active Directory** (Подключение Active Directory) щелкните **Добавить каталог** и выполните вход с учетной записью администратора домена Active Directory.  Примечание. Учетная запись администратора домена не должна иметь требований к изменению пароля. Если пароль изменится или истечет срок его действия, вам придется повторно настроить новые учетные данные в агенте. Эта операция добавляет локальный каталог.  Щелкните **Далее**.</br>
![Экран приветствия](media/how-to-install/install3.png)</br>

9. На странице **Конфигурация завершена** щелкните **Подтвердить**.  Эта операция регистрирует и перезапускает агент.</br>
![Экран приветствия](media/how-to-install/install4.png)</br>

10. После завершения операции появится сообщение: **Your agent configuration was successfully verified** (Конфигурация агента успешно проверена).  Здесь можно щелкнуть **Выйти**.</br>
![Экран приветствия](media/how-to-install/install5.png)</br>
11. Если вы по-прежнему видите начальный экран-заставку, щелкните **Закрыть**.


## <a name="verify-agent-installation"></a>Проверка установки агента
Проверка агента выполняется на портале Azure и на локальном сервере, где выполняется агент.

### <a name="azure-portal-agent-verification"></a>Проверка агента на портале Azure
Чтобы проверить, что агент доступен в Azure, сделайте следующее:

1. Войдите на портал Azure.
2. Слева выберите **Azure Active Directory**, щелкните **Azure AD Connect** и в центре выберите **Управление подготовкой (предварительная версия)** .</br>
![портал Azure](media/how-to-install/install6.png)</br>

3.  На экране **Подготовка Azure AD (предварительная версия)** щелкните **Проверить все агенты**.
![Подготовка Azure AD](media/how-to-install/install7.png)</br>
 
4. На экране **локальных агентов подготовки** вы увидите установленные агенты.  Убедитесь, что нужный агент присутствует в этом списке с пометкой **active** (Активный).
![Подготовка агентов](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>На локальном сервере
Чтобы убедиться, что агент выполняется, сделайте следующее.

1.  Войдите на сервер с учетной записью администратора.
2.  Откройте страницу **Службы**, перейдя к ней в интерфейсе или выполнив команду Services.msc в строке запуска из меню "Пуск".
3.  В разделе **Services** (Службы) убедитесь, что **Microsoft Azure AD Connect Agent Updater** (Агент обновления Microsoft Azure AD Connect) и **Microsoft Azure AD Connect Provisioning Agent** (Агент подготовки Microsoft Azure AD Connect) присутствуют в списке и имеют состояние **Running** (Выполняется).
![Службы](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Настройка процесса подготовки облака Azure AD Connect
 Чтобы настроить подготовку, выполните следующие действия.

1.  Войдите на портал Azure AD.
2.  В меню **Azure Active Directory**
3.  Щелкните **Azure AD Connect**.
4.  Выберите **Управление подготовкой (предварительная версия)** 
![](media/how-to-configure/manage1.png).
5.  Щелкните **Новая конфигурация**
![](media/tutorial-single-forest/configure1.png).
7.  На экране конфигурации перейдите к разделу **Сообщение электронной почты с уведомлением**, переместите селектор в положение **Включить** и щелкните **Сохранить**.
![](media/tutorial-single-forest/configure2.png)
1.  Теперь состояние конфигурации должно быть **Healthy** (Работоспособное).
![](media/how-to-configure/manage4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Проверка создания пользователей и выполнения синхронизации
Теперь вы проверите, были ли пользователи из локального каталога синхронизированы и добавлены в клиент Azure AD.  Имейте в виду, что эта операция может занять несколько часов.  Чтобы проверить синхронизацию пользователей, выполните указанные ниже действия.


1. Перейдите на [портал Azure](https://portal.azure.com) и выполните вход с учетной записью, имеющей подписку Azure.
2. Слева выберите **Azure Active Directory**.
3. В разделе **Управление** выберите **Пользователи**.
4. Убедитесь в том, что новый пользователь отображается в клиенте.</br>
![Синхронизация](media/tutorial-single-forest/synchronize1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Проверка входа с помощью одной из учетных записей

1. Перейдите на сайт [https://myapps.microsoft.com](https://myapps.microsoft.com).
2. Выполните вход с помощью учетной записи пользователя, которая была создана в новом клиенте.  Для этого следует использовать следующий формат: (user@domain.onmicrosoft.com). Используйте тот же пароль, что и для входа в локальную среду.</br>
   ![Проверка](media/tutorial-single-forest/verify1.png)</br>

Вы успешно настроили среду гибридной идентификации, которую можно использовать для тестирования и ознакомления с возможностями Azure.

## <a name="next-steps"></a>Дополнительная информация 

- [Что собой представляет подготовка?](what-is-provisioning.md)
- [What is Azure AD Connect cloud provisioning?](what-is-cloud-provisioning.md) (Что такое подготовка облака Azure AD Connect?)
