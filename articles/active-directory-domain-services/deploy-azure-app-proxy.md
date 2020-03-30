---
title: Развертывание прокси-сервера Azure AD для служб доменов Azure AD (ru) Документы Майкрософт
description: Узнайте, как обеспечить безопасный доступ к внутренним приложениям для удаленных работников путем развертывания и настройки прокси-сервера приложения active Directory Proxy в домене Сугранс активных служб управления каталогами Azure Active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/6/2019
ms.author: iainfou
ms.openlocfilehash: c6e4e6a45fbbeab64184d8ae4b0684ba055d7735
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613987"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Развертывание прокси-приложения Azure AD для безопасного доступа к внутренним приложениям в домене Azure AD Domain

С помощью служб доменов Azure AD (Azure AD DS) можно перевести устаревшие приложения, работающие на базе Azure. Затем прокси-сервер приложения Azure Active Directory (AD) поможет вам поддержать удаленных работников, надежно опубликовав эти внутренние приложения, часть домена Azure AD DS, чтобы к ним можно было получить доступ через Интернет.

Если вы новичок в приложении Azure AD Proxy и хотите узнать больше, см. [Как обеспечить безопасный удаленный доступ к внутренним приложениям.](../active-directory/manage-apps/application-proxy.md)

В этой статье показано, как создать и настроить разъем прокси-сервер Azure AD Application Для обеспечения безопасного доступа к приложениям в домене Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Перед началом

Для завершения этой статьи необходимы следующие ресурсы и привилегии:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, создайте [учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
    * Для использования прокси-приложения Azure AD-приложения требуется **лицензия Azure AD Premium.**
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * Если потребуется, [создайте и настройте экземпляр доменных служб Azure Active Directory][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Создание домена, связанного с Windows VM

Чтобы направить трафик в приложения, работающие в среде, вы устанавливаете компонент разъема azure AD Application Proxy. Этот разъем прокси-сервер Azure AD Должно быть установлен на виртуальных компьютерах Windows Server (VM), которые соединены с доменом Azure AD DS. Для некоторых приложений можно развернуть несколько серверов, каждый из которых установлен разъем. Этот параметр развертывания обеспечивает более высокую доступность и позволяет обрабатывать более тяжелые нагрузки аутентификации.

VM, который работает с разъемом Azure AD Application Proxy, должен находиться в той же виртуальной сети, в которой вы включили Azure AD DS. Виртуальные сети Azure, которые затем размещают приложения, которые вы публикуете с помощью прокси-сервера приложения, также должны быть развернуты в той же виртуальной сети Azure.

Чтобы создать VM для прокси-разъема Azure AD, выполните следующие шаги:

1. [Создайте настраиваемое подразделение](create-ou.md). Можно делегировать разрешения на управление этим пользовательским OU пользователям в домене Azure AD DS. VMs для Прокси-приложения Azure AD и запущенные приложения должны быть частью пользовательского OU, а не *AAD DC Computers* OU.
1. [Домен присоединяется к виртуальным машинам,][create-join-windows-vm]как к разъему Azure AD Application Proxy, так и к тем, которые управляются приложениями, к домену Azure AD DS. Создайте эти учетные записи компьютера в пользовательском OU от предыдущего шага.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Скачать разъем прокси-сервер приложения Azure AD

Выполните следующие шаги, чтобы загрузить разъем прокси-сервер Azure AD. Загружаемый файл настройки скопирован в приложение Proxy VM в следующем разделе.

1. Вопийте на [порталaz](https://portal.azure.com) с учетной записью пользователя с разрешениями *администратора предприятия* в Azure AD.
1. Поиск и выбор **Active Directory Azure** в верхней части портала, а затем выберите **приложения Enterprise.**
1. Выберите **прокси-сервер приложения** из меню на левой стороне. Чтобы создать свой первый разъем и включить Прокси-сервер приложения, выберите **ссылку, чтобы загрузить разъем.**
1. На странице загрузки примите условия лицензии и соглашение о конфиденциальности, а затем выберите **Условия «Принять & Скачать.**

    ![Скачать разъем прокси-сервер Azure AD App](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Установка и регистрация разъема прокси-сервера Приложения Azure AD

С vM, готовым к использованию в качестве разъема proxy приложения Azure AD, теперь копируйте и запускайте файл настройки, загруженный с портала Azure.

1. Копируйте файл настройки подключения приложения Azure AD Для вашего VM.
1. Выполнить файл настройки, например *AADApplicationProxyConnectorInstaller.exe*. Примите условия лицензионного соглашения.
1. Во время установки вам предлагается зарегистрировать разъем с помощью прокси-сервера приложения в каталоге Azure AD.
   * Предоставьте учетные данные для глобального администратора в каталоге Azure AD. Учетные данные администратора администратора Azure AD могут отличаться от учетных данных Azure на портале

        > [!NOTE]
        > Учетная запись глобального администратора, используемая для регистрации разъема, должна принадлежать к тому же каталогу, где вы включите службу Application Proxy.
        >
        > Например, если домен Azure AD *находится aaddscontoso.com,* глобальный администратор должен быть `admin@aaddscontoso.com` или другим действительным псевдонимом в этом домене.

   * Если конфигурация расширенной безопасности Internet Explorer включена для VM, где вы устанавливаете разъем, экран регистрации может быть заблокирован. Чтобы обеспечить доступ, следуйте инструкциям в сообщении об ошибке или выключите Internet Explorer Enhanced Security во время процесса установки.
   * Если регистрация разъема не удается, [см.](../active-directory/manage-apps/application-proxy-troubleshoot.md)
1. В конце установки отображается примечание для сред с исходящим прокси. Чтобы настроить разъем прокси-сервер Azure AD Application для работы через `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`исходящий прокси, запустите предоставленный скрипт, например
1. На странице прокси Приложения на портале Azure новый разъем указан со статусом *Active,* как показано в следующем примере:

    ![Новый разъем прокси-сервер Azure AD, работающий на портале Azure](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Чтобы обеспечить высокую доступность для приложений, удостоверяющих подлинность через прокси-приложение Azure AD, можно установить разъемы на нескольких всах. Повторите те же шаги, перечисленные в предыдущем разделе, чтобы установить разъем на других серверах, объединенных с доменом Azure AD DS.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Включить ресурсо-ориентированную делегацию Kerberos с ограниченным

Если вы хотите использовать одиночный набор для своих приложений с помощью интегрированной аутентификации Windows (IWA), предоставьте разъему связи Azure AD Application Proxy для выдачи себя за пользователей и отправки и получения токенов от их имени. Чтобы предоставить эти разрешения, вы настраиваете ограниченную делегацию Kerberos (KCD) для разъема для доступа к ресурсам в домене Azure AD DS. Поскольку у вас нет привилегий администратора домена в домене Azure AD DS, традиционный KCD уровня учетной записи не может быть настроен на управляемый домен. Вместо этого используйте ресурсо-ориентированный KCD.

Для получения дополнительной [Configure Kerberos constrained delegation (KCD) in Azure Active Directory Domain Services](deploy-kcd.md)информации см.

> [!NOTE]
> Вы должны быть подписаны на учетную запись пользователя, которая является членом *группы администраторов Azure AD В* вашем арендаторе Azure AD для выполнения следующих cmdlets PowerShell.
>
> Компьютер должен учитывать ваш разъем Для App Proxy VM и VMs-приложения, должен находиться в пользовательском OU, где у вас есть разрешения на настройку на основе ресурсов KCD. Вы не можете настроить ресурсо-налажено KCD для учетной записи компьютера во встроенном контейнере *AAD DC Computers.*

Используйте [Get-ADComputer][Get-ADComputer] для получения настроек для компьютера, на котором установлен разъем прокси-сервер Azure AD. Из совместного управления VM, связанного с доменом, и вошел в систему в качестве учетной записи пользователя, являющуюся членом *группы администраторов Azure AD DC,* запустите следующие cmdlets.

Следующий пример получает информацию о учетной записи компьютера под названием *appproxy.aaddscontoso.com*. Предоставьте свое собственное имя компьютера для прокси-приложения Azure AD Proxy VM, настроенного на предыдущих этапах.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Для каждого сервера приложений, запускаемых приложениями за Azure AD Application Proxy, используйте cmdlet [Set-ADComputer][Set-ADComputer] PowerShell для настройки ресурсо-ориентированного KCD. В следующем примере разъем прокси-сервер azure AD Application получает разрешение на использование *appserver.aaddscontoso.com* компьютера:

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

При развертывании нескольких разъемов Azure AD Application Proxy необходимо настроить ресурсо-ориентированный KCD для каждого экземпляра разъема.

## <a name="next-steps"></a>Дальнейшие действия

С помощью приложения Azure AD Proxy, интегрированного с Azure AD DS, публикуйте приложения для доступа пользователей. Для получения дополнительной информации смотрите [публикации приложений с помощью прокси-приложения Azure AD.](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
