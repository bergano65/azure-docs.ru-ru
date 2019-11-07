---
title: Развертывание AD Application Proxy Azure для доменных служб Azure AD | Документация Майкрософт
description: Узнайте, как обеспечить безопасный доступ к внутренним приложениям для удаленных рабочих ролей путем развертывания и настройки Azure Active Directory Application Proxy в управляемом домене Azure Active Directory доменных служб.
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
ms.openlocfilehash: c0fcb8c2c5f9afa7fabe2ffa63a715ec24aa4a26
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720500"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Развертывание AD Application Proxy Azure для безопасного доступа к внутренним приложениям в управляемом домене доменных служб Azure AD

С помощью доменных служб Azure AD (Azure AD DS) вы можете перемещать устаревшие приложения, работающие локально, в Azure. Затем прокси приложения Azure Active Directory (AD) помогает поддерживать удаленные рабочие роли, обеспечивая безопасную публикацию внутренних приложений в управляемом домене AD DS Azure, чтобы к ним можно было получить доступ через Интернет.

Если вы не знакомы с AD Application Proxy Azure и хотите узнать больше, ознакомьтесь со [статьей как обеспечить безопасный удаленный доступ к внутренним приложениям](../active-directory/manage-apps/application-proxy.md).

В этой статье показано, как создать и настроить соединитель Azure AD Application Proxy для обеспечения безопасного доступа к приложениям в управляемом домене Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Перед началом работы

Для работы с этой статьей необходимы следующие ресурсы и привилегии:

* Активная подписка Azure.
    * Если у вас еще нет подписки Azure, создайте [учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure Active Directory со своей учетной записью][associate-azure-ad-tenant].
    * Для использования AD Application Proxy Azure требуется **Лицензия на Azure AD Premium** .
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте AAD.
    * Если потребуется, [создайте и настройте экземпляр доменных служб Azure Active Directory][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Создание присоединенной к домену виртуальной машины Windows

Чтобы направить трафик в приложения, работающие в вашей среде, установите компонент соединителя Azure AD Application Proxy. Этот соединитель Azure AD Application Proxy должен быть установлен на виртуальных машинах Windows Server, присоединенных к управляемому домену AD DS Azure. Для некоторых приложений можно развернуть несколько серверов, на каждом из которых установлен соединитель. Этот параметр развертывания обеспечивает более высокую доступность и позволяет обрабатывать более тяжелые нагрузки аутентификации.

Виртуальная машина, на которой работает соединитель Azure AD Application Proxy, должна быть в той же или в одноранговой виртуальной сети, в которой вы включили AD DS Azure. Виртуальные машины, на которых затем размещены приложения, публикуемые с помощью прокси приложения, также должны быть развернуты в одной виртуальной сети Azure.

Чтобы создать виртуальную машину для соединителя Azure AD Application Proxy, выполните следующие действия.

1. [Создайте настраиваемое подразделение](create-ou.md). Вы можете делегировать разрешения на управление этим пользовательским подразделением пользователям в управляемом домене Azure AD DS. Виртуальные машины для AD Application Proxy Azure и, которые работают с приложениями, должны быть частью пользовательского подразделения, а не подразделения по умолчанию " *Компьютеры AAD DC* ".
1. [Присоедините к домену виртуальные машины][create-join-windows-vm], в которых выполняется соединитель Azure AD application proxy, и те, которые запускают ваши приложения, в управляемый домен AD DS Azure. Создайте учетные записи этих компьютеров в пользовательском подразделении из предыдущего шага.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Скачайте соединитель Azure AD Application Proxy

Чтобы скачать соединитель Azure AD Application Proxy, выполните следующие действия. Скачанный файл установки копируется на виртуальную машину прокси приложения в следующем разделе.

1. Войдите в [портал Azure](https://portal.azure.com) с учетной записью пользователя, обладающей разрешениями *администратора предприятия* в Azure AD.
1. Найдите и выберите **Azure Active Directory** в верхней части портала, а затем выберите **корпоративные приложения**.
1. Выберите **прокси приложения** в меню в левой части. Чтобы создать первый соединитель и включить прокси приложения, щелкните ссылку, чтобы **скачать соединитель**.
1. На странице загрузки примите условия лицензии и соглашения о конфиденциальности, а затем выберите **принять условия & скачать**.

    ![Скачивание соединителя прокси-сервера Azure AD App](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Установка и регистрация соединителя Azure AD Application Proxy

Теперь, когда виртуальная машина готова к использованию в качестве соединителя Azure AD Application Proxy, скопируйте и запустите файл установки, скачанный из портал Azure.

1. Скопируйте файл установки соединителя Azure AD Application Proxy на виртуальную машину.
1. Запустите файл установки, например *аадаппликатионпроксиконнекторинсталлер. exe*. Примите условия лицензионного соглашения.
1. Во время установки вам будет предложено зарегистрировать соединитель с помощью прокси приложения в каталоге Azure AD.
   * Укажите учетные данные глобального администратора в каталоге Azure AD. Учетные данные глобального администратора Azure AD могут отличаться от учетных данных Azure на портале.

        > [!NOTE]
        > Учетная запись глобального администратора, используемая для регистрации соединителя, должна принадлежать тому же каталогу, в котором включена служба прокси приложения.
        >
        > Например, если домен Azure AD — *contoso.com*, глобальный администратор должен `admin@contoso.com` или другой допустимый псевдоним в этом домене.

   * Если для виртуальной машины, в которой устанавливается соединитель, включена конфигурация усиленной безопасности Internet Explorer, экран регистрации может быть заблокирован. Чтобы разрешить доступ, следуйте инструкциям в сообщении об ошибке или отключите усиленную безопасность Internet Explorer во время процесса установки.
   * Если регистрация соединителя завершается неудачно, см. раздел [Устранение неполадок прокси приложения](../active-directory/manage-apps/application-proxy-troubleshoot.md).
1. В конце установки для сред с исходящим прокси-сервером отображается Примечание. Чтобы настроить соединитель Azure AD Application Proxy для работы через исходящий прокси-сервер, запустите предоставленный сценарий, например `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`.
1. На странице прокси приложения в портал Azure новый соединитель отображается с состоянием *активно*, как показано в следующем примере:

    ![Новый соединитель Azure AD Application Proxy, отображаемый как активный в портал Azure](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Чтобы обеспечить высокий уровень доступности для приложений, прошедших проверку подлинности в Azure AD Application Proxy, соединители можно установить на нескольких виртуальных машинах. Повторите те же действия, перечисленные в предыдущем разделе, чтобы установить соединитель на других серверах, присоединенных к управляемому домену Azure AD DS.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Включить ограниченное делегирование Kerberos на основе ресурсов

Если вы хотите использовать единый вход для приложений, использующих встроенную проверку подлинности Windows (IWA), предоставьте соединителям Azure AD Application Proxy разрешения на олицетворение пользователей и отправку и получение маркеров от их имени. Чтобы предоставить эти разрешения, необходимо настроить ограниченное делегирование Kerberos (KCD) для доступа к ресурсам в управляемом домене Azure AD DS. Так как у вас нет прав администратора домена в управляемом домене Azure AD DS, нельзя настроить традиционные KCD на уровне учетной записи в управляемом домене. Вместо этого используйте KCD на основе ресурсов.

Дополнительные сведения см. [в статье Настройка ограниченного делегирования Kerberos (KCD) в доменных службах Azure Active Directory](deploy-kcd.md).

> [!NOTE]
> Для выполнения следующих командлетов PowerShell необходимо войти в учетную запись пользователя, которая является членом группы " *Администраторы контроллера домена Azure AD* " в клиенте Azure AD.
>
> Учетные записи компьютеров для виртуальной машины соединителя прокси приложения и виртуальных машин приложений должны находиться в настраиваемом подразделении, где имеются разрешения на настройку KCD на основе ресурсов. Невозможно настроить KCD на основе ресурсов для учетной записи компьютера во встроенном контейнере " *Компьютеры AAD DC* ".

Используйте [Get-ADComputer][Get-ADComputer] , чтобы получить параметры для компьютера, на котором установлен соединитель Azure AD application proxy. На виртуальной машине управления, присоединенной к домену, и войдите в систему под учетной записью пользователя, которая является членом группы *администраторов контроллера домена Azure AD* , выполните следующие командлеты.

В следующем примере показано получение сведений о учетной записи компьютера с именем *appproxy.contoso.com*. Укажите собственное имя компьютера для виртуальной машины Azure AD Application Proxy, настроенной на предыдущих шагах.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.contoso.com
```

Для каждого сервера приложений, на котором работают приложения AD Application Proxy Azure, используйте командлет PowerShell [Set-ADComputer][Set-ADComputer] , чтобы настроить KCD на основе ресурсов. В следующем примере для соединителя Azure AD Application Proxy предоставляются разрешения на использование компьютера *appserver.contoso.com* :

```powershell
Set-ADComputer appserver.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

При развертывании нескольких соединителей Azure AD Application Proxy необходимо настроить KCD на основе ресурсов для каждого экземпляра соединителя.

## <a name="next-steps"></a>Дальнейшие действия

С помощью Azure AD Application Proxy, интегрированной с Azure AD DS, опубликуйте приложения для доступа пользователей. Дополнительные сведения см. в статье [Публикация приложений с помощью Azure AD application proxy](../active-directory/manage-apps/application-proxy-publish-azure-portal.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
