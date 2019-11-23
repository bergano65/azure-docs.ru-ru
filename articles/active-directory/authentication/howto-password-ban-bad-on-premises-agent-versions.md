---
title: Password protection agent release history - Azure Active Directory
description: Содержит журнал выпуска версий и изменения в поведении.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6458f31b21d09e54afb080ffc73598903a9831e0
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381730"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>журнал выпуска версий агента защиты паролем Azure AD

## <a name="121250"></a>1.2.125.0

Release date: 3/22/2019

* Fix minor typo errors in event log messages
* Update EULA agreement to final General Availability version

> [!NOTE]
> Build 1.2.125.0 is the General Availability build. Thank you again to everyone has provided feedback on the product!

## <a name="121160"></a>1.2.116.0

Release date: 3/13/2019

* The Get-AzureADPasswordProtectionProxy and Get-AzureADPasswordProtectionDCAgent cmdlets now report software version and the current Azure tenant with the following limitations:
  * Software version and Azure tenant data are only available for DC agents and proxies running version 1.2.116.0 or later.
  * Azure tenant data may not be reported until a re-registration (or renewal) of the proxy or forest has occurred.
* The Proxy service now requires that .NET 4.7 is installed.
  * .NET 4.7 should already be installed on a fully updated Windows Server. If this is not the case, download and run the installer found at [The .NET Framework 4.7 offline installer for Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * On Server Core systems it may be necessary to pass the /q flag to the .NET 4.7 installer to get it to succeed.
* The Proxy service now supports automatic upgrade. Automatic upgrade uses the Microsoft Azure AD Connect Agent Updater service which is installed side-by-side with the Proxy service. Automatic upgrade is on by default.
* Automatic upgrade can be enabled or disabled using the Set-AzureADPasswordProtectionProxyConfiguration cmdlet. The current setting can be queried using the Get-AzureADPasswordProtectionProxyConfiguration cmdlet.
* The service binary for the DC agent service has been renamed to AzureADPasswordProtectionDCAgent.exe.
* The service binary for the Proxy service has been renamed to AzureADPasswordProtectionProxy.exe. Firewall rules may need to be modified accordingly if a third-party firewall is in-use.
  * NOTE: if an http proxy config file was being used in a previous Proxy install, it will need to be renamed (from *proxyservice.exe.config* to *AzureADPasswordProtectionProxy.exe.config*) after this upgrade.
* All time-limited functionality checks have been removed from the DC agent.
* Minor bugs fixes and logging improvements.

## <a name="12650"></a>1.2.65.0

Release date: 2/1/2019

Изменения:

* Агент и прокси-сервер службы контроллера домена теперь поддерживаются на основном сервере. Mininimum OS requirements are unchanged from before: Windows Server 2012 for DC agents, and Windows Server 2012 R2 for proxies.
* Командлеты Register-AzureADPasswordProtectionProxy и Register-AzureADPasswordProtectionForest теперь поддерживают режимы проверки подлинности Azure на основе кода устройства.
* Командлет Get-AzureADPasswordProtectionDCAgent проигнорирует искаженные и недопустимые точки подключения службы. Это исправляет ошибку, при которой контроллеры домена иногда появлялись несколько раз в выходных данных.
* Командлет Get-AzureADPasswordProtectionSummaryReport проигнорирует искаженные и (или) недопустимые точки подключения службы. Это исправляет ошибку, при которой контроллеры домена иногда появлялись несколько раз в выходных данных.
* Модуль прокси-сервера PowerShell теперь зарегистрирован в папке %ProgramFiles%\WindowsPowerShell\Modules. Переменная среды PSModulePath компьютера больше не изменяется.
* Добавлен новый командлет Get-AzureADPasswordProtectionProxy, чтобы помочь в обнаружении зарегистрированных прокси-серверов в лесу или домене.
* Агент контроллера домена использует новую папку в общей папке sysvol для репликации политик паролей и других файлов.

   Старое расположение папки:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Новое расположение папки:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Это изменение было внесено, чтобы избежать ложных срабатываний предупреждений о "потерянных объектах групповой политики").

   > [!NOTE]
   > Миграция или обмен данными не будут выполняться между старой и новой папкой. Старые версии агента контроллера домена продолжат использовать старое расположение до тех пор, пока не будут обновлены до этой версии или более поздней. После запуска всех агентов контроллера домена версии 1.2.65.0 или более поздней версии, старую папку sysvol можно удалить вручную.

* Агент и прокси-сервер службы контроллера домена теперь будут обнаруживать и удалять искаженные копии соответствующих точек подключения службы.
* Каждый агент контроллера домена будет периодически удалять искаженные и устаревшие точки подключения службы в своем домене как для агента контроллера домена, так и для точек подключения службы прокси-сервера. Точки подключения к агенту контроллера сервера и прокси-серверу считаются устаревшими, если его метка времени превышает семь дней.
* Агент контроллера домена теперь будет обновлять сертификат леса по мере необходимости.
* Служба прокси-сервера теперь будет обновлять сертификат прокси-сервера по мере необходимости.
* Обновления алгоритма проверки пароля: список глобально заблокированных паролей и список заблокированных паролей для конкретного клиента (если настроено) объединяются перед проверкой пароля. Данный пароль теперь можете отклонить (сбой или только аудит), если он содержит токены как из глобального, так и из клиентского списка. Чтобы это изобразить, была обновлена документация журнала событий; см. [Preview: Azure AD Password Protection monitoring and logging](howto-password-ban-bad-on-premises-monitor.md) (Предварительный просмотр: мониторинг и ведение журнала защиты паролем Azure AD).
* Исправления, повышающие производительность и надежность
* Улучшенное ведение журнала.

> [!WARNING]
> Ограниченная функциональность: служба агента контроллера домена в этом выпуске (1.2.65.0) прекратит обработку запросов на проверку пароля с 1 сентября 2019 года.  Службы агента контроллера домена в предыдущих выпусках (см. список ниже) остановят обработку с 1 июля 2019 года. Служба агента контроллера домена во всех версиях внесет 10 021 событие в журнал событий администратора в течение двух месяцев до этих крайних сроков. Все ограничения по времени будут удалены в следующем выпуске общедоступной версии. Не существует ограничений времени ни в одной версии службы агента прокси-сервера, но ее все равно необходимо обновить до последней версии, чтобы воспользоваться всеми последующими исправлениями ошибок и другими улучшениями.

## <a name="12250"></a>1.2.25.0

Дата выпуска: 11.01.2018

Исправления:

* Исправлено аварийное завершение работы агента контроллера домена и служба прокси-сервера из-за проблем доверия к сертификату.
* Внесены дополнительные исправления в агент контроллера домена и службу прокси-сервера для компьютеров, соответствующих требованиям FIPS.
* Теперь служба прокси-сервера правильно работает в сетевых средах, поддерживающих только TLS 1.2.
* Небольшие исправления, повышающие производительность и надежность.
* Улучшенное ведение журнала.

Изменения:

* Теперь минимальная требуемая версия ОС для службы прокси-сервера — Windows Server 2012 R2. Для службы агента контроллера домена по-прежнему требуется как минимум Windows Server 2012.
* Для службы прокси-сервера теперь необходимо .NET версии 4.6.2.
* Алгоритм проверки паролей использует расширенную таблицу для нормализации символов. Это может привести к тому, что пароли, которые принимались в старых версиях, теперь будут отвергаться.

## <a name="12100"></a>1.2.10.0

Дата выпуска: 17.08.2018

Исправления:

* Register-AzureADPasswordProtectionProxy и Register-AzureADPasswordProtectionForest теперь поддерживают многофакторную проверку подлинности.
* Для использования Register-AzureADPasswordProtectionProxy домен должен использовать контроллер домена под управлением Windows Server 2012 или более поздней версии, чтобы избежать ошибок шифрования.
* Повышена надежность службы агента контроллера домена при запросе новой политики паролей из Azure во время запуска.
* Служба агента контроллера домена будет запрашивать новую политику паролей из Azure каждый час, если это необходимо, но теперь она это будет делать со случайно выбранным временем начала.
* Служба агента контроллера домена больше не будет вызывать неопределенную задержку при объявлении нового контроллера домена, если он устанавливается на сервер до его повышения до реплики.
* Теперь служба агента контроллера домена будет поддерживать параметр конфигурации "Включить защиту паролем на Windows Server Active Directory".
* Теперь установщик агента контроллера домена и установщик прокси-сервера будут поддерживать обновление на месте до будущих версий.

> [!WARNING]
> Обновление на месте версии 1.1.10.3 не поддерживается и приведет к ошибке при установке. Для обновления до версии 1.2.10 или более поздней версии необходимо сначала полностью удалить программное обеспечение агента контроллера домена и прокси-службы, а затем установить новую версию с нуля. Повторная регистрация прокси-службы защиты паролем Azure AD является обязательной.  Не требуется повторно регистрировать лес.

> [!NOTE]
> Для обновления на месте программного обеспечения агента контроллера домена потребуется перезагрузка.

* Агент контроллера домена и прокси-служба теперь поддерживают выполнение на сервере, настроенном для использования только FIPS-совместимых алгоритмов.
* Небольшие исправления, повышающие производительность и надежность.
* Улучшенное ведение журнала.

## <a name="11103"></a>1.1.10.3

Дата выпуска: 15.06.2018

Первоначальный выпуск общедоступной предварительной версии

## <a name="next-steps"></a>Дальнейшие действия

[Развертывание защиты паролем Azure AD (предварительная версия)](howto-password-ban-bad-on-premises-deploy.md)
