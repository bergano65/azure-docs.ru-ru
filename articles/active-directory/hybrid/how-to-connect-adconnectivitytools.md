---
title: 'Azure AD Connect выполняет следующие функции: Модуль PowerShell ADConnectivityTools | Документация Майкрософт'
description: В этом документе представлены новый модуль ADConnectivity PowerShell и как его можно использовать для устранения неполадок.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5340cd8c802df4ffbe0207b5401d2fee4e207e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571118"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Устранение неполадок подключения Azure AD с помощью модуля ADConnectivityTool PowerShell

Инструмент ADConnectivity представляет собой модуль PowerShell, который используется в одном из следующих случаев.

- Во время установки при успешной проверке предотвращает ли неполадки сетевого подключения Active Directory учетные данные пользователя, указанный в мастере.
- После установки используется пользователем, который вызывает функции из сеанса PowerShell.

Средство находится здесь: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1**. 

## <a name="adconnectivitytool-during-installation"></a>Использование ADConnectivityTool во время установки

В случае сбоя сети при работе со страницей **Connect your directories** (Подключение каталогов) в мастере Azure AD Connect инструмент ADConnectivityTool будет автоматически использовать одну из своих функций, чтобы определить, что происходит.  Ниже приведены ситуации, которые могут быть определены как сбои сети.

- Пользователь неправильно ввел имя леса или этот лес не существует. 
- UDP-порт 389 закрыт в контроллерах домена, связанных с лесом, указанным пользователем.
- Учетные данные, введенные в окне "Учетная запись леса AD", не имеют привилегий для получения контроллеров домена, связанных с целевым лесом.
- TCP-порт 53, 88 или 389 закрыт в контроллерах домена, связанных с лесом, указанным пользователем. 
- Закрыты UDP-порт 389 и TCP-порт (или TCP-порты).
- Не удалось разрешить DNS-имя для указанного леса и (или) связанных с ним контроллеров домена.

При обнаружении какой-либо из этих проблем в окне мастера AAD Connect отображается сообщении об ошибке.


![Ошибка](media/how-to-connect-adconnectivitytools/error1.png)

Например, когда мы пытаемся добавить каталог на странице **Connect your directories** (Подключение каталогов), служба Azure AD Connect должна проверить его. Для этого ей требуется возможность взаимодействия с контроллером домена через порт 389.  Если это невозможно, мы увидим ошибку, как показано на снимке экрана выше.  

Что происходит на самом деле: Azure AD Connect вызывает функцию `Start-NetworkConnectivityDiagnosisTools`.  Эта функция вызывается в том случае, если проверка учетных данных не пройдена из-за проблемы с сетевым подключением.

Наконец, каждый раз, когда этот инструмент вызывается с помощью мастера, создается подробный файл журнала. Log, расположенный в **C:\ProgramData\AADConnect\ADConnectivityTool-\<даты >-\<времени > .log**

## <a name="adconnectivitytools-post-installation"></a>Использование ADConnectivityTools после установки
После установки Azure AD Connect можно использовать любую из функций в модуле PowerShell ADConnectivityTools.  

Дополнительные сведения об этих функциях можно найти в [справочнике по ADConnectivityTools](reference-connect-adconnectivitytools.md).

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Мы вызовем эту функцию, так как ее можно вызвать **только** вручную после импорта ADConnectivityTool.psm1 в PowerShell. 

Эта функция выполняет ту же логику, которую мастер Azure AD Connect использует, чтобы проверить предоставленные учетные данные AD.  Тем не менее она предоставляет гораздо более подробное описание проблемы и предлагаемого решения. 

Проверка подключения состоит из следующих шагов.
-   Получите объект DomainFQDN (полное доменное имя).
-   Если пользователь выбрал "Создайте учетную запись AD", то проверьте, принадлежат ли эти учетные данные к группе администраторов предприятия.
-   Получите объект ForestFQDN.
-   Проверьте, доступен ли по крайней мере один домен, связанный с ранее полученным объектом ForestFQDN.
-   Убедитесь, что функциональный уровень леса — Windows Server 2003 или более поздняя версия.

Пользователь будет иметь возможность добавить каталог, если все эти действия были успешно выполнены.

Если пользователь выполняет эту функцию после устранения проблемы (или если проблем вообще не было), в выходных данных будет указано, что пользователь может вернуться в мастер Azure AD Connect и попробовать еще раз добавить учетные данные.



## <a name="next-steps"></a>Следующие шаги
- [Azure AD Connect: учетные записи и разрешения](reference-connect-accounts-permissions.md)
- [Приступая к работе с Azure AD Connect с использованием стандартных параметров](how-to-connect-install-express.md)
- [Выборочная установка Azure AD Connect](how-to-connect-install-custom.md)
- [Справочник по ADConnectivityTools](reference-connect-adconnectivitytools.md)

