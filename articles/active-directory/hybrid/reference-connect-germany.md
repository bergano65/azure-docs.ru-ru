---
title: Azure AD Connect в Microsoft Cloud для Германии
description: Azure AD Connect интегрирует локальные каталоги с Azure Active Directory. Таким образом вы сможете предоставить пользователям возможность получать доступ с использованием одного удостоверения для Office 365, Azure и SaaS, интегрированных с Azure AD.
keywords: введение в Azure AD Connect, обзор Azure AD Connect, что такое Azure AD Connect, установка Аctive Directory, Германия, Шварцвальд
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 5ccfab71edf9f5fd61191ae8bd4ef5421a055e23
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164628"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Общедоступная предварительная версия Azure AD Connect в Microsoft Cloud для Германии
## <a name="introduction"></a>Введение
Azure AD Connect обеспечивает синхронизацию локальных каталогов Active Directory с каталогами Azure Active Directory.
В настоящее время многие сценарии в [Microsoft Cloud для Германии](https://azure.microsoft.com/global-infrastructure/germany/
) должны выполняться оператором. При использовании Microsoft Cloud для Германии важно учитывать следующее:

* Для успешного выполнения синхронизации указанные ниже URL-адреса должны быть открыты через прокси-сервер:
  
  * *.microsoftonline.de
  * *.windows.net
  * * списки отзыва сертификатов.
* При входе в каталог Azure AD необходимо использовать учетную запись в домене onmicrosoft.de.

 
## <a name="download"></a>Download (Скачать)
Вы можете загрузить Azure AD Connect из колонки Azure AD Connect на портале.  Чтобы найти колонку Azure AD Connect, следуйте указанным ниже инструкциям.

### <a name="the-azure-ad-connect-blade"></a>Колонка Azure AD Connect
Войдите на портал Azure.

1. Щелкните "Обзор".
2. Выберите Azure Active Directory.
3. Затем выберите Azure AD Connect.

Вы увидите следующее:

![Колонка Azure AD Connect](./media/reference-connect-germany/germany1.png)

Следующая таблица содержит описание функций, представленных в колонке.

| Название | ОПИСАНИЕ |
| --- | --- |
| Состояние синхронизации |Сообщает, включена ли синхронизация. |
| Последняя синхронизация |Показывает, когда в последний раз была успешно выполнена синхронизация. |
| Федеративные домены |Показывает текущее количество настроенных федеративных доменов. |

## <a name="installation"></a>Установка
Чтобы установить Azure AD Connect, воспользуйтесь [этой документацией](how-to-connect-install-roadmap.md).

## <a name="advanced-features-and-additional-information"></a>Дополнительные функции и дополнительные сведения
Сведения о пользовательских и расширенных настройках см. в руководстве по [интеграции локальных удостоверений с Azure Active Directory](whatis-hybrid-identity.md). На этой странице представлена необходимая информация и ссылки на дополнительные инструкции.

