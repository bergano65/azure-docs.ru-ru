---
title: Устранение неполадок при установке Azure AD Connect | Документация Майкрософт
description: В этой статье приводятся пошаговые инструкции для устранения неполадок при установке Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 56fd4be85fabebcdde5ca271630e1c5b83c398b9
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772740"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Устранение неполадок, Проблемы с установкой Azure AD Connect

## <a name="recommended-steps"></a>**Рекомендуемые действия**
Убедитесь, что [тип установки Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) подходит для вас. Если возникло условие экспресс-установки, то мы настоятельно рекомендуем перейти к экспресс-установке. Экспресс-установка предоставляет минимальные параметры, необходимые для завершения установки, поэтому существует меньше вероятности возникновения проблем. 

Тем не менее, если условия экспресс-установки не возникают и вам необходимо выполнить выборочную установку, ознакомьтесь с приведенными ниже рекомендациями, которые помогут вам избежать распространенных проблем. Для простоты здесь упомянуты только выборочные параметры.

* Убедитесь, что вы являетесь администратором на компьютере, где устанавливается AAD Connect. Войдите на компьютер под этими же учетными данными администратора.

* Оставьте значения по умолчанию для всех параметров на следующей странице, за исключением параметра "Использовать существующий SQL Server", если вы хотите использовать существующий SQL Server. Дополнительные сведения об использовании параметров выборочной установки см. [здесь](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom). 

    ![Параметр "Использовать существующий SQL Server"](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* На следующей странице выберите вариант "Создайте учетную запись AD", чтобы избежать любых проблем с разрешениями, связанными с существующей учетной записью.

    ![Учетная запись леса AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Распространенные проблемы**

* [Проблемы с подключением к локальной службе Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Проблемы с подключением к веб-службе Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Проблемы с разрешениями для локальной службы Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Рекомендуемые документы**
* [Необходимые условия для Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Выбор типа установки для Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Приступая к работе с Azure AD Connect с использованием стандартных параметров](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Выборочная установка Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: обновление до последней версии](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: Что такое промежуточный сервер?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Что такое модуль PowerShell ADConnectivityTool?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Дополнительная информация
- [Синхронизация Azure AD Connect: общие сведения о синхронизации и ее настройка](how-to-connect-sync-whatis.md)
- [Что собой представляет гибридная идентификация](whatis-hybrid-identity.md)





