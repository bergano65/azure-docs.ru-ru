---
title: Управление DNS для служб доменов Azure AD (ru) Документы Майкрософт
description: Узнайте, как установить DNS Server Tools для управления DNS для домена Сугеи Active Directory Domain Services, управляемого.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 2694a5f250b746748a1b42ac4d211aa28ef1ebad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613697"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Администрирование DNS в домене домена Azure AD, управляемом доменом

В службах активных доменов Azure Active Directory Domain Services (Azure AD DS) ключевым компонентом является DNS (Разрешение доменных имен). Azure AD DS включает dNS-сервер, который обеспечивает разрешение имен для управляемого домена. Этот DNS-сервер включает встроенные DNS-записи и обновления ключевых компонентов, которые позволяют работать службе.

При запуске собственных приложений и служб может потребоваться создать Записи DNS для машин, которые не соединены с доменом, настроить виртуальные IP-адреса для балансеров нагрузки или настроить внешние дисторы DNS. Пользователям, входящим в группу *администраторов AAD DC,* предоставляются привилегии администрирования DNS в домене Azure AD DS, а также могут создавать и отсеивать пользовательские DNS-записи.

В гибридной среде зоны и записи DNS, настроенные в предприимчезную среду AD DS, не синхронизированы с Azure AD DS. Чтобы определить и использовать собственные записи DNS, создайте записи на сервере Azure AD DS DNS или используйте условные форварды, указывающие на существующие DNS-серверы в вашей среде.

В этой статье показано, как установить инструменты DNS Server, а затем использовать консоль DNS для управления записями в Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Перед началом

Для завершения этой статьи необходимы следующие ресурсы и привилегии:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, создайте [учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * При необходимости заполните учебник для [создания и настройки экземпляра служб доменов Active Directory Службы Azure.][create-azure-ad-ds-instance]
* VM управления Windows Server, который соединен с доменом Azure AD DS.
    * При необходимости заполните учебник для [создания Windows Server VM и присоединитесь к управляемому домену.][create-join-windows-vm]
* Учетная запись пользователя, входящая в группу *администраторов Azure AD DC* в клиенте Azure AD.

## <a name="install-dns-server-tools"></a>Установка инструментов DNS Server

Для создания и изменения DNS-записей в DD Azure AD необходимо установить инструменты DNS Server. Эти инструменты могут быть установлены в качестве функции в Windows Server. Для получения дополнительной информации о том, как установить административные инструменты на клиенте Windows, [см.][install-rsat]

1. Вопийте в своем менеджменте VM. Для нас можно ознакомиться с информацией о том, как подключиться с помощью портала Azure, [см.][connect-windows-server-vm]
1. Если **диспетчер сервера** не открывается по умолчанию при входе в виртуальную машину, откройте меню **Пуск**, а затем выберите **Диспетчер сервера**.
1. На *панели управления* в окне **диспетчера серверов** щелкните **Добавить роли и компоненты**.
1. На странице **Перед началом работы** в *мастере добавления ролей и компонентов* щелкните **Далее**.
1. В разделе *Тип установки* оставьте флажок **Установка ролей или компонентов** и щелкните **Далее**.
1. На странице **выбора сервера** выберите текущий VM из пула серверов, например *myvm.aaddscontoso.com,* затем выберите **Следующий.**
1. На странице **Роли сервера** нажмите кнопку **Далее**.
1. На странице **Компоненты** разверните узел **Средства удаленного администрирования сервера**, а затем узел **Средства администрирования ролей**. Из списка средств администрирования ролей выберите компонент **Средства DNS-сервера**.

    ![Выберите установку DNS Server Tools из списка доступных инструментов управления ролевой доделами](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. На странице **Подтверждение** щелкните **Установить**. Установка инструментов управления политикой группы может занять минуту или две.
1. Когда установка компонента завершится, нажмите кнопку **Закрыть**, чтобы выйти из мастера **добавления ролей и компонентов**.

## <a name="open-the-dns-management-console-to-administer-dns"></a>Откройте консоль управления DNS для администрирования DNS

С установленными инструментами DNS Server можно управлять DNS-записями в домене Azure AD DS.

> [!NOTE]
> Для администрирования DNS в домене Azure AD DS необходимо ввести в систему учетную запись пользователя, являющиеся членом *группы администраторов AAD DC.*

1. С начального экрана выберите **Административные инструменты.** Отображается список доступных инструментов управления, включая **DNS,** установленные в предыдущем разделе. Выберите **DNS** для запуска консоли DNS Management.
1. В диалоге **Connect to DNS Server** выберите **следующий компьютер,** затем введите доменное имя DNS управляемого домена, например *aaddscontoso.com:*

    ![Подключение к домену Azure AD DS, управляемому DNS на консоли DNS](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. Консоль DNS подключается к указанному домену Azure AD DS. Расширьте **зоны поиска вперед** или **зоны обратного поиска,** чтобы создать необходимые записи DNS или отредконить существующие записи по мере необходимости.

    ![Консоль DNS — администрирование домена](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> При управлении записями с помощью инструментов DNS Server убедитесь, что вы не удалите и не измените встроенные DNS-записи, используемые DD DS Azure AD. Встроенные записи DNS включают в себя записи DNS домена, записи сервера имен и другие записи, используемые для обнаружения контроллера домена. Изменение этих записей приведет к нарушению работы доменных служб в виртуальной сети.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об управлении DNS см. в статье [о средствах DNS на сайте TechNet](https://technet.microsoft.com/library/cc753579.aspx).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
