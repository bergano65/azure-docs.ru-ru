---
title: Смена секретов в Azure Stack | Документация Майкрософт
description: Дополнительные сведения о смене секретов в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 27fd02503881ef1f0587ccb0301f8490101d5bcb
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720651"
---
# <a name="rotate-secrets-in-azure-stack"></a>Смена секретов в Azure Stack

*Эти инструкции применяются только к интегрированным системам Azure Stack (1803 и более поздних версий). Не пытайтесь сменить секреты в версиях Azure Stack, предшествующих 1802.*

Azure Stack использует различные секреты, чтобы обеспечивать безопасный обмен данными между службами и ресурсами инфраструктуры Azure Stack.

- **Внутренние секреты**  
Все сертификаты, пароли, защищенные строки и ключи, используемые инфраструктурой Azure Stack без вмешательства оператора Azure Stack. 

- **Внешние секреты**  
Сертификаты службы инфраструктуры для внешних служб, предоставляемых оператором Azure Stack. Сюда входят сертификаты для следующих служб: 
  - портал администрирования;  
  - Общедоступный портал  
  - Azure Resource Manager для администратора;  
  - глобальный Azure Resource Manager;  
  - хранилище ключей администратора;  
  - Хранилище ключей  
  - Хост-процесс для расширений администратора  
  - ACS (включая хранилище BLOB-объектов, таблиц и очередей);  
  - ADFS *  
  - Graph *  

\* Применимо, только если поставщиком удостоверений среды являются службы федерации Active Directory (AD FS).

> [!Note]  
> Все остальные защищенные ключи и строки, включая пароли BMC и коммутатора, пароли учетной записи пользователя и администратора, по-прежнему вручную обновляются администратором. 

> [!Note]  
> Начиная с выпуска Azure Stack 1811, смены секретов разделены для внутренних и внешних сертификатов. 

Чтобы поддерживать целостность инфраструктуры Azure Stack, операторам необходима возможность периодически сменять секреты своей инфраструктуры с частотой, соответствующей требованиям безопасности организации.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Смена секретов с помощью внешних сертификатов из нового центра сертификации

Azure Stack поддерживает смену секретов с помощью внешних сертификатов из нового сертификации (ЦС) в следующих контекстах.

|Установленный ЦС|Целевой ЦС для смены секрета|Поддерживаются|Поддерживаемые версии Azure Stack|
|-----|-----|-----|-----|
|С самозаверяющими сертификатами|Корпоративный|Не поддерживается||
|С самозаверяющими сертификатами|С самозаверяющими сертификатами|Не поддерживается||
|С самозаверяющими сертификатами|Общедоступный<sup>*</sup>|Поддерживаются|1803 и более поздние версии|
|Корпоративный|Корпоративный|Поддерживается, если клиенты используют ТОТ ЖЕ корпоративный ЦС, что и во время развертывания.|1803 и более поздние версии|
|Корпоративный|С самозаверяющими сертификатами|Не поддерживается||
|Корпоративный|Общедоступный<sup>*</sup>|Поддерживаются|1803 и более поздние версии|
|Общедоступный<sup>*</sup>|Корпоративный|Не поддерживается|1803 и более поздние версии|
|Общедоступный<sup>*</sup>|С самозаверяющими сертификатами|Не поддерживается||
|Общедоступный<sup>*</sup>|Общедоступный<sup>*</sup>|Поддерживаются|1803 и более поздние версии|

<sup>*</sup>Указывает, что общедоступными называются центры сертификации, которые являются частью программы доверенных корневых центров сертификации Windows. Вы можете ознакомиться с полным списком в статье [Microsoft Trusted Root Certificate Program: Participants (as of June 27, 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca) (Список участников программы доверенных корневых центров сертификации Майкрософт по состоянию на 27 июня 2017 г.).

## <a name="alert-remediation"></a>Исправление оповещений

Когда до истечения срока действия секретов осталось 30 дней, на портале администратора появляются следующие оповещения: 

- ожидается истечение срока действия пароля учетной записи службы; 
- ожидается истечение срока действия внутреннего сертификата; 
- ожидается истечение срока действия внешнего сертификата. 

Выполнение смены секрета с помощью описанных ниже инструкций устранит эти оповещения.

> [!Note]  
> В средах Azure Stack до версии 1811 могут возникать предупреждения об ожидающих обработки внутренних сертификатах или окончании срока действия секретов. Эти предупреждения являются неточными. Их следует игнорировать без выполнения смены внутренних секретов. Неточные предупреждения об окончании срока действия внутренних секретов являются известной проблемой, решенной в версии 1811, — срок действия секретов не истекает, за исключением случаев, когда среда была активна два года. 

## <a name="pre-steps-for-secret-rotation"></a>Предварительные шаги для смены секретов

   > [!IMPORTANT]  
   > Если смена секретов уже выполнена в среде Azure Stack, обновите систему до версии 1811 или более поздней, прежде чем еще раз выполнить смену секретов. Смена секретов выполняется через [привилегированную конечную точку](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) и требует учетные данные оператора Azure Stack. Если операторы вашей среды Azure Stack не знают, выполнялась ли смена секретов в вашей среде, обновите систему до версии 1811, прежде чем снова выполнить смену секретов.

1. Настоятельно рекомендуется обновить ваш экземпляр Azure Stack до версии 1811.

    > [!Note] 
    > В версиях до 1811 вам не нужно выполнять смену секретов для добавления сертификатов хост-процесса для расширений. Следуйте инструкциям в статье [Подготовка хост-процесса для расширений для Azure Stack](azure-stack-extension-host-prepare.md), чтобы добавить сертификаты хост-процесса для расширений.

2.  Операторы могут заметить, как во время смены секретов Azure Stack появляются и автоматически закрываются предупреждения.  Такое поведение считается нормальным и предупреждения можно игнорировать.  Операторы могут проверить действительность этих предупреждений, выполнив команду **Test-AzureStack**.  Если операторы, использующие SCOM для мониторинга систем Azure Stack, переведут системы в режим обслуживания, предупреждения не будут достигать систем ITSM, но будут появляться, если система Azure Stack станет недоступной. 
3. Уведомите пользователей об операциях обслуживания. Запланируйте стандартные окна обслуживания, по возможности в нерабочие часы. Операции технического обслуживания могут влиять на рабочие нагрузки пользователей и на операции портала.

    > [!Note]  
    > Следующие шаги применяются только при смене внешних секретов Azure Stack.

4. Перед сменой секретов запустите **[Test-AzureStack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)** и убедитесь, что все выходные данные тестов работоспособны.
5. Подготовьте новый набор внешних сертификатов для замены. Новый набор соответствует спецификациям сертификата, приведенным в статье [Требования к сертификатам инфраструктуры открытых ключей Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs). Вы можете создать запрос подписи сертификата (CSR), чтобы приобрести или создать новые сертификаты, следуя инструкциям в статье [Создание сертификатов PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-get-pki-certs), и подготовить их к использованию в вашей среде Azure Stack, следуя инструкциям в статье [Подготовка сертификатов PKI Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-prepare-pki-certs). Обязательно проверьте подготавливаемые сертификаты, следуя инструкциям в статье [Проверка сертификатов PKI](https://docs.microsoft.com/azure/azure-stack/azure-stack-validate-pki-certs). 
6.  Сохраните резервную копию в сертификаты, используемые для смены, в защищенном расположении. Если смена выполняется, а затем завершается со сбоем, замените сертификаты в общей папке резервными копиями перед повторным запуском смены. Обратите внимание, что резервные копии необходимо сохранить в безопасном расположении.
7.  Создайте общую папку, к которой можно получить доступ из виртуальных машин ERCS. Общая папка должна быть доступна для чтения и записи для удостоверения **CloudAdmin**.
8.  Откройте консоль интегрированной среды сценариев PowerShell на компьютере с доступом к общей папке. Перейдите к общей папке. 
9.  Запустите файл **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)**, чтобы создать требуемые каталоги для внешних сертификатов.

## <a name="rotating-external-secrets"></a>Смена внешних секретов

Чтобы выполнить смену внешних секретов, сделайте следующее.

1. В каталоге **/Certificates**, созданном в рамках предварительных шагов, установите новый набор внешних сертификатов для замены в структуре каталога в соответствии с форматом, описанным в разделе "Обязательные сертификаты" статьи [Требования к сертификатам инфраструктуры открытых ключей Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Создайте сеанс PowerShell с [привилегированной конечной точкой](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) с использованием учетной записи **CloudAdmin** и сохраните сеансы в качестве переменных. Эта переменная будет использоваться как параметр в следующем шаге.

    > [!IMPORTANT]  
    > Не входите в сеанс, сохраните его как переменную.
    
3. Выполните команду **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Передайте переменную сеанса PowerShell с привилегированной конечной точкой в качестве параметра **Сеанс**. 
4. Выполните команду **Start-SecretRotation** со следующими параметрами:
    - **PfxFilesPath**  
    Укажите сетевой путь к созданному ранее каталогу сертификатов.  
    - **PathAccessCredential**  
    Объект PSCredential для учетных данных в общей папке. 
    - **CertificatePassword**  
    Защищенная строка пароля, используемая для всех созданных файлов сертификатов PFX.
4. Дождитесь смены секретов. Смена внешних секретов обычно занимает около часа. Когда смена сертификатов будет успешно выполнена, в консоли появится следующее: **Overall action status: Success** (Общее состояние действия: успешно выполнено) 
    > [!Note]  
    > Если не удается выполнить смену секретов, следуйте инструкциям в сообщении об ошибке и повторно запустите командлет **start-secretrotation** с параметром **-Rerun**. 

    ```PowerShell  
    Start-SecretRotation -Rerun
    ```
    Обратитесь в службу поддержки, если при смене секретов постоянно возникают сбои.
5. После успешного завершения смены секретов удалите свои сертификаты из общего ресурса, созданного на предварительном шаге, и сохраните их в безопасном расположении резервного копирования. 

## <a name="walkthrough-of-secret-rotation"></a>Пошаговое руководство по смене секретов

Следующий пример PowerShell демонстрирует командлеты и параметры, необходимые для смены секретов.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>Смена только внутренних секретов

> [!Note]  
> Смену внутренних секретов следует выполнять только в том случае, если вы подозреваете, что внутренний секрет был скомпрометирован вредоносным объектом, или если вы получили оповещение (в версии 1811 или более поздней), указывающее, что срок действия внутренних сертификатов скоро истечет. В средах Azure Stack до версии 1811 могут возникать предупреждения об ожидающих обработки внутренних сертификатах или окончании срока действия секретов. Эти предупреждения являются неточными. Их следует игнорировать без выполнения смены внутренних секретов. Неточные предупреждения об окончании срока действия внутренних секретов являются известной проблемой, решенной в версии 1811, — срок действия секретов не истекает, за исключением случаев, когда среда была активна два года.

Чтобы сменить только внутренние секреты Azure Stack, сделайте следующее.

1. Создайте сеанс PowerShell с [привилегированной конечной точкой](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. В сеансе с привилегированной конечной точкой выполните **Start-SecretRotation -Internal**.

    > [!Note]  
    > Среды Azure Stack версий до 1811 не требуют флаг **-Internal**. Командлет **Start-SecretRotation** сменяет только внутренние секреты.

3. Дождитесь смены секретов.  
Когда смена сертификатов будет успешно выполнена, в консоли появится следующее: **Overall action status: Success** (Общее состояние действия: успешно выполнено) 
    > [!Note]  
    > Если не удается выполнить смену секретов, следуйте инструкциям в сообщении об ошибке и повторно запустите командлет **start-secretrotation** с параметрами **–Internal** и  **-Rerun**.  

    ```PowerShell  
    Start-SecretRotation -Internal -Rerun
    ```
    Обратитесь в службу поддержки, если при смене секретов постоянно возникают сбои.

## <a name="start-secretrotation-reference"></a>Справочник по Start-SecretRotation

Этот командлет сменяет секреты системы Azure Stack. Выполняется только с привилегированной конечной точкой Azure Stack.

### <a name="syntax"></a>Синтаксис

#### <a name="for-external-secret-rotation"></a>Для смены внешних секретов

```Powershell  
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>Для смены внутренних секретов 

```Powershell  
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>Для повторной смены внешних секретов 

```Powershell  
Start-SecretRotation [-Rerun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>Для повторной смены внутренних секретов 

```Powershell  
Start-SecretRotation [-Rerun] [-Internal]
```
### <a name="description"></a>ОПИСАНИЕ

Командлет **Start-SecretRotation** сменяет секреты инфраструктуры системы Azure Stack. По умолчанию он сменяет только сертификаты всех конечных точек инфраструктуры внешней сети. При использовании флага -Internal будут сменяться внутренние секреты инфраструктуры. При смене конечных точек инфраструктуры внешней сети командлет **Start-SecretRotation** необходимо выполнить через блок скрипта **Invoke-Command** в сеансе с привилегированной конечной точкой среды Azure Stack, переданном в качестве параметра сеанса.
 
### <a name="parameters"></a>Параметры

| Параметр | type | Обязательно | Позиция | значение по умолчанию | ОПИСАНИЕ |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Строка  | Ложь  | именованная  | Нет  | Путь к общей папке в каталоге **\Certificates**, который содержит все сертификаты конечных точек внешней сети. Требуется только при смене внешних секретов или всех секретов. Конечным каталогом должен быть **\Certificates**. |
| CertificatePassword | SecureString | Ложь  | именованная  | Нет  | Пароль для всех сертификатов, предоставляемых в -PfXFilesPath. Необходимое значение, если PfxFilesPath предоставлен, когда сменяются внешние и внутренние секреты. |
| Внутренний | Строка | Ложь | именованная | Нет | Флаг -Internal должен использоваться каждый раз, когда оператор Azure Stack хочет сменить внутренние секреты инфраструктуры. |
| PathAccessCredential | PSCredential | Ложь  | именованная  | Нет  | Учетные данные PowerShell для общего ресурса каталога **\Certificates**, который содержит все сертификаты конечных точек внешней сети. Требуется только при смене внешних секретов или всех секретов.  |
| Rerun | SwitchParameter | Ложь  | именованная  | Нет  | Повторное выполнение необходимо использовать при каждой повторной попытке смены секретов после неудачи. |

### <a name="examples"></a>Примеры
 
#### <a name="rotate-only-internal-infrastructure-secrets"></a>Смена только секретов внутренней инфраструктуры

Ее необходимо выполнять с помощью [привилегированной конечной точки среды](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) Azure Stack.

```powershell  
PS C:\> Start-SecretRotation  -Internal
```

Эта команда сменяет все инфраструктурные секреты, предоставляемые внутренней сети Azure Stack. 

#### <a name="rotate-only-external-infrastructure-secrets"></a>Смена только секретов внешней инфраструктуры  

```powershell  
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock {  

Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }  

Remove-PSSession -Session $PEPSession
```

Эта команда сменяет сертификаты TSL, используемые для конечных точек инфраструктуры внешней сети Azure Stack.   

**Смена секретов внешней и внутренней инфраструктуры**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Эта команда сменяет все секреты инфраструктуры, предоставляемые во внутренней сети Azure Stack, а также сертификаты TSL, используемые конечными точками инфраструктуры внешней сети Azure Stack. Командлет Start-SecretRotation сменяет все созданные стеком секреты и, так как сертификаты предоставлены, сертификаты внешних конечных точек.  


## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Обновление учетных данных контроллера управления основной платой (BMC)

Контроллер управления основной платой отслеживает физическое состояние серверов. Спецификации и инструкции по обновлению пароля и имени учетной записи пользователя контроллера управления основной платой различаются в зависимости от поставщика вычислительной техники. Следует регулярно обновлять пароли для компонентов Azure Stack.

1. Обновите контроллер управления основной платой на физических серверах Azure Stack в соответствии с инструкциями поставщика. Пароли и имена учетной записи пользователя для всех контроллеров в среде должны совпадать.
2. Откройте привилегированную конечную точку в сеансах Azure Stack. См. инструкции по [использованию привилегированной конечной точки в Azure Stack](azure-stack-privileged-endpoint.md).
3. После изменения запроса PowerShell на **[IP-адрес или имя виртуальной машины ERCS]: PS>** или на **[azs-ercs01]: PS>** (в зависимости от среды) выполните `Set-BmcCredemtial`, запустив `invoke-command`. Передайте переменную сеанса привилегированной конечной точки в качестве параметра. Например: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBMCuser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BMCPassword is mandatory, while the BMCUser parameter is optional.
        Set-Bmccredential -bmcpassword $using:NewBMCpwd -bmcuser $using:NewBMCuser
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    Также можно использовать статическую версию PowerShell с паролями в качестве строк кода:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -Force
    $NewBMCuser = "<New BMC User name>" 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmccredential -bmcpassword $using:NewBMCpwd -bmcuser $using:NewBMCuser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Дополнительная информация

[Система безопасности для инфраструктуры Azure Stack](azure-stack-security-foundations.md)
