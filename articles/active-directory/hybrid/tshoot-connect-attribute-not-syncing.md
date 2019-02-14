---
title: Устранение неполадок атрибута, связанных с отсутствием синхронизации в Azure AD Connect | Документация Майкрософт
description: В этой статье приводятся пошаговые инструкции по устранению неполадок, связанных с синхронизацией атрибутов, с помощью задач устранения неполадок.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180831"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Устранение неполадок атрибута, связанных с отсутствием синхронизации в Azure AD Connect

## <a name="recommended-steps"></a>**Рекомендуемые действия**

Прежде чем исследовать проблемы синхронизации атрибутов, давайте разберемся с процессом синхронизации **Azure AD Connect**.

  ![Процесс синхронизации Azure AD Connect](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Терминология**

* **CS** — пространство соединителя, таблица в базе данных.
* **MV** — метавселенная, таблица в базе данных.
* **AD** — Active Directory
* **AAD** — Azure Active Directory

### <a name="synchronization-steps"></a>**Шаги синхронизации**

* Импорт из AD. Объекты Active Directory переносятся в CS AD.

* Импорт из AAD. Объекты Azure Active Directory переносятся в CS AAD.

* Синхронизация. **Правила входящей синхронизации** и Правила исходящей синхронизации выполняются в порядке номера приоритета от меньшего к большему. Чтобы просмотреть правила синхронизации, можно перейти в **Редактор правил синхронизации** из приложений рабочего стола. **Правила входящей синхронизации** перемещают данные из CS в MV. **Правила исходящей синхронизации** перемещают данные из MV в CS.

* Экспорт в AD. После выполнения синхронизации объекты экспортируются из CS AD в **Active Directory**.

* Экспорт в AAD. После выполнения синхронизации объекты экспортируются из CS AAD в **Azure Active Directory**.

### <a name="step-by-step-investigation"></a>**Пошаговое исследование**

* Мы начнем поиск из **метавселенной** и рассмотрим сопоставление атрибутов между источником и целевым объектом.

* Запустите **Synchronization Service Manager** из приложений на рабочем столе, как показано ниже:

  ![Запуск средства Synchronization Service Manager](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* В **Synchronization Service Manager** щелкните **Metaverse Search** (Поиск в метавселенной), **Scope by Object Type** (Определить область по типу объекта), выберите объект с помощью атрибута, а затем нажмите кнопку **Поиск**.

  ![Параметр поиска в метавселенной](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Дважды щелкните объект, обнаруженный в результате поиска **в метавселенной**, чтобы просмотреть его атрибуты. Вы можете щелкнуть вкладку **Соединители**, чтобы просмотреть соответствующий объект во всех **пространствах соединителей**.

  ![Соединители объекта метавселенной](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Дважды щелкните **соединитель Active Directory**, чтобы просмотреть атрибуты **пространства соединителя**. Нажмите кнопку **Предварительный просмотр**. В открывшемся диалоговом окне нажмите кнопку **Generate Preview** (Создать предварительный просмотр).

  ![Атрибуты пространства соединителя](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Теперь щелкните **Import Attribute Flow** (Поток атрибутов импорта). Этот параметр позволяет просмотреть поток атрибутов из **пространства соединителя Active Directory** в **метавселенную**. В столбце **Sync Rule** (Правило синхронизации) показано, какое **правило синхронизации** влияет на этот атрибут. В столбце **Источник данных** показаны атрибуты из **пространства соединителя**. Столбец **Metaverse Attribute** (Атрибут метавселенной) содержит атрибуты, находящиеся в **метавселенной**. В нем вы можете найти атрибут, который не синхронизируется. Если вам не удается найти атрибут в этом столбце, это значит, что он не сопоставлен. Чтобы сопоставить этот атрибут, вам необходимо создать настраиваемое **правило синхронизации**.

  ![Атрибуты пространства соединителя](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Щелкните **Export Attribute Flow** (Поток атрибутов экспорта) в области слева, чтобы просмотреть обратный поток атрибутов из **метавселенной** в **пространство соединителя Active Directory**, использующий **правила исходящей синхронизации**.

  ![Атрибуты пространства соединителя](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Аналогичным образом вы можете просмотреть объект **пространства соединителя Azure Active Directory** и создать **предварительный просмотр**, чтобы просмотреть поток атрибутов из **метавселенной** в **пространство соединителя** и обратно. Таким образом вы можете выяснить, почему атрибут не синхронизируется.

## <a name="recommended-documents"></a>**Рекомендуемые документы**
* [Синхронизация Azure AD Connect: технические понятия](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Синхронизация Azure AD Connect: основные сведения об архитектуре](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Синхронизация Azure AD Connect: общие сведения о декларативной подготовке](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Синхронизация Azure AD Connect: общие сведения о выражениях декларативной подготовки](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Синхронизация Azure AD Connect: общие сведения о конфигурации по умолчанию](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Синхронизация Azure AD Connect: общие сведения о пользователях, группах и контактах](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Синхронизация Azure AD Connect: теневые атрибуты](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Дальнейшие действия

- [Синхронизация Azure AD Connect: общие сведения о синхронизации и ее настройка](how-to-connect-sync-whatis.md).
- [Что собой представляет гибридная идентификация](whatis-hybrid-identity.md).
