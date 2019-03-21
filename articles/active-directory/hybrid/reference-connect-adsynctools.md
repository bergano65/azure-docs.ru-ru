---
title: 'Azure AD Connect выполняет следующие функции: Справочник по модулю PowerShell ADSyncTools | Документация Майкрософт'
description: Этот документ содержит справочные сведения о модуле PowerShell ADSyncTools.psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a1b8abf15233c06e8ff9e507b315cc8a3703970
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443340"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect выполняет следующие функции:  Справочник по модулю PowerShell ADSyncTools
Приведенная ниже документация содержит справочные сведения о модуле PowerShell ADSyncTools.psm1, который входит в состав Azure AD Connect.

## <a name="clear-adsynctoolsconsistencyguid"></a>Clear-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>Краткий обзор
Удаление пользователя AD из mS-Ds-ConsistencyGuid

### <a name="syntax"></a>Синтаксис

```
Clear-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>Описание
Очистка значения в mS-Ds-ConsistencyGuid для целевого пользователя AD.

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>Пример 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>Параметры

#### <a name="-user"></a>-User
Целевой пользователь в AD, которого следует настроить.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-adsynctoolsadmoduleloaded"></a>Confirm-ADSyncToolsADModuleLoaded

### <a name="synopsis"></a>Краткий обзор
{{Введите краткий обзор}}

### <a name="syntax"></a>Синтаксис

```
Confirm-ADSyncToolsADModuleLoaded
```

### <a name="description"></a>Описание
{{Введите описание}}

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Здесь добавьте пример описания }}

## <a name="connect-adsyncdatabase"></a>Connect-AdSyncDatabase

### <a name="synopsis"></a>Краткий обзор
{{Введите краткий обзор}}

### <a name="syntax"></a>Синтаксис

```
Connect-AdSyncDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>] [[-UserName] <String>]
 [[-Password] <String>] [<CommonParameters>]
```

### <a name="description"></a>Описание
{{Введите описание}}

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Здесь добавьте пример описания }}

### <a name="parameters"></a>Параметры

#### <a name="-database"></a>-Database
{{Введите описание базы данных}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-instance"></a>-Instance
{{Введите описание экземпляра}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-password"></a>-Password
{{Введите описание пароля}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
{{Введите описание сервера}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName
{{Введите описание имени пользователя}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="export-adsynctoolsconsistencyguidmigration"></a>Export-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>Краткий обзор
Экспорт отчета ConsistencyGuid.

### <a name="syntax"></a>Синтаксис

```
Export-ADSyncToolsConsistencyGuidMigration [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```

### <a name="description"></a>Описание
Создает отчет ConsistencyGuid на основе CSV-файла, импортированного с помощью Import-ADSyncToolsImmutableIdMigration.

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsConsistencyGuidMigration -Output ".\AllSyncUsers-Report"
```

#### <a name="example-2"></a>Пример 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>Параметры

#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Использование альтернативного идентификатора для входа (почты).

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
Значение ImmutableIdGUID.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
Имя для выходных CSV- и LOG-файлов.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsyncsqlbrowserinstances"></a>Get-ADSyncSQLBrowserInstances

### <a name="synopsis"></a>Краткий обзор
{{Введите краткий обзор}}

### <a name="syntax"></a>Синтаксис

```
Get-ADSyncSQLBrowserInstances [[-hostName] <String>]
```

### <a name="description"></a>Описание
{{Введите описание}}

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Здесь добавьте пример описания }}

### <a name="parameters"></a>Параметры

#### <a name="-hostname"></a>-hostName
{{Введите описание имени узла}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="get-adsynctoolsaduser"></a>Get-ADSyncToolsADuser

### <a name="synopsis"></a>Краткий обзор
Получение пользователя из AD.

### <a name="syntax"></a>Синтаксис

```
Get-ADSyncToolsADuser [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>Описание
Возвращает объект AD TO DO. Поддерживается несколько лесов.

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>Пример 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>Параметры

#### <a name="-user"></a>-User
Целевой пользователь в AD для настройки ConsistencyGuid.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsconsistencyguid"></a>Get-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>Краткий обзор
Получение mS-Ds-ConsistencyGuid пользователя AD.

### <a name="syntax"></a>Синтаксис

```
Get-ADSyncToolsConsistencyGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>Описание
Возвращает значение атрибута mS-Ds-ConsistencyGuid целевого пользователя AD в формате GUID.

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>Пример 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>Параметры

#### <a name="-user"></a>-User
Целевой пользователь в AD, которого следует настроить.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsobjectguid"></a>Get-ADSyncToolsObjectGuid

### <a name="synopsis"></a>Краткий обзор
Получение ObjectGuid пользователя AD.

### <a name="syntax"></a>Синтаксис

```
Get-ADSyncToolsObjectGuid [-User] <Object> [<CommonParameters>]
```

### <a name="description"></a>Описание
Возвращает значение атрибута ObjectGuid целевого пользователя AD в формате GUID.

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>Пример 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>Параметры

#### <a name="-user"></a>-User
Целевой пользователь в AD, которого следует настроить.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory

### <a name="synopsis"></a>Краткий обзор
Получение журнала выполнения AAD Connect.

### <a name="syntax"></a>Синтаксис

```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```

### <a name="description"></a>Описание
Функция, возвращающая журнал выполнения AAD Connect в формате XML.

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Get-ADSyncToolsRunHistory
```

#### <a name="example-2"></a>Пример 2
```
Get-ADSyncToolsRunHistory -Days 1
```

### <a name="parameters"></a>Параметры

#### <a name="-days"></a>-Days
{{Введите описание дней}}

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 3
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-adsynctoolssourceanchorchanged"></a>Get-ADSyncToolsSourceAnchorChanged

### <a name="synopsis"></a>Краткий обзор
Получение пользователей с ошибками изменения SourceAnchor.

### <a name="syntax"></a>Синтаксис

```
Get-ADSyncToolsSourceAnchorChanged [-sourcePath] <Object> [-outputPath] <Object> [<CommonParameters>]
```

### <a name="description"></a>Описание
Функция запрашивает журнал выполнения AAD Connect и экспортирует всех пользователей с сообщением об ошибке: SourceAnchor attribute has changed (Атрибут SourceAnchor изменен).

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
#Required Parameters
```

$sourcePath = Read-Host -Prompt "Enter your log file path with file name" #"\<Source_Path\>" $outputPath = Read-Host -Prompt "Enter your out file path with file name" #"\<Out_Path\>"
 
 Get-ADSyncToolsUsersSourceAnchorChanged -sourcePath $sourcePath -outputPath $outputPath

#### <a name="example-2"></a>Пример 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>Параметры

#### <a name="-sourcepath"></a>-sourcePath
{{Введите описание пути к источнику}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-outputpath"></a>-outputPath
{{Введите описание пути к выходному файлу}}

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="import-adsynctoolsimmutableidmigration"></a>Import-ADSyncToolsImmutableIdMigration

### <a name="synopsis"></a>Краткий обзор
Импорт значения ImmutableID из AAD.

### <a name="syntax"></a>Синтаксис

```
Import-ADSyncToolsImmutableIdMigration [-Output] <String> [-IncludeSyncUsersFromRecycleBin]
 [<CommonParameters>]
```

### <a name="description"></a>Описание
Создает файл со всеми синхронизированными пользователями Azure AD, содержащий значение ImmutableID в формате GUID. Требования: модуль PowerShell MSOnline.

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Import-ADSyncToolsImmutableIdMigration -OutputFile '.\AllSyncUsers.csv'
```

#### <a name="example-2"></a>Пример 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>Параметры

#### <a name="-output"></a>-Output
Выходной CSV-файл.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Получение синхронизированных пользователей из корзины Azure AD.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


## <a name="invoke-adsyncdatabasequery"></a>Invoke-AdSyncDatabaseQuery

### <a name="synopsis"></a>Краткий обзор
{{Введите краткий обзор}}

### <a name="syntax"></a>Синтаксис

```
Invoke-AdSyncDatabaseQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```

### <a name="description"></a>Описание
{{Введите описание}}

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Здесь добавьте пример описания }}

### <a name="parameters"></a>Параметры

#### <a name="-query"></a>-Query
{{Введите описание запроса}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-sqlconnection"></a>-SqlConnection
{{Введите описание SqlConnection}}

```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>Краткий обзор
Сценарий для удаления просроченных сертификатов из атрибута UserCertificate.

### <a name="syntax"></a>Синтаксис

```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```

### <a name="description"></a>Описание
Этот сценарий принимает все объекты из целевого подразделения в домене Active Directory, отфильтрованные по классу объекта (пользователь или компьютер), и удаляет все просроченные сертификаты, присутствующие в атрибуте UserCertificate.
По умолчанию (в режиме BackupOnly) он будет только создавать резервную копию просроченных сертификатов и записывать ее в файл, не внося каких-либо изменений в AD.
Если используется параметр -BackupOnly $false, то любой просроченный сертификат в атрибуте UserCertificate для этих объектов будет удален из AD после копирования в файл.
Каждый сертификат будет сохранен в отдельный файл резервной копии с именем вида: КлассОбъекта_GUIDОбъекта_ОтпечатокСертификата.cer. Сценарий также создаст файл журнала в формате CSV, содержащий всех пользователей с сертификатами (действительными или просроченными), включая фактическое выполненное действие (пропуск, экспорт, удаление).

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass user

#### <a name="example-2"></a>Пример 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```

Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false

### <a name="parameters"></a>Параметры

#### <a name="-targetou"></a>-TargetOU
Целевое подразделение для поиска объектов AD.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-backuponly"></a>-BackupOnly
Параметр BackupOnly позволяет не удалять какие-либо сертификаты из AD.

```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-objectclass"></a>-ObjectClass
Фильтр класса объектов.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState

### <a name="synopsis"></a>Краткий обзор
Краткое описание

### <a name="syntax"></a>Синтаксис

```
Repair-ADSyncToolsAutoUpgradeState
```

### <a name="description"></a>Описание
Подробное описание

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>Пример 2
```
Another example of how to use this cmdlet
```

## <a name="resolve-adsynchostaddress"></a>Resolve-ADSyncHostAddress

### <a name="synopsis"></a>Краткий обзор
{{Введите краткий обзор}}

### <a name="syntax"></a>Синтаксис

```
Resolve-ADSyncHostAddress [[-hostName] <String>]
```

### <a name="description"></a>Описание
{{Введите описание}}

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Здесь добавьте пример описания }}

### <a name="parameters"></a>Параметры

#### <a name="-hostname"></a>-hostName
{{Введите описание имени узла}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="restore-adsynctoolsexpiredcertificates"></a>Restore-ADSyncToolsExpiredCertificates

### <a name="synopsis"></a>Краткий обзор
(ЧТОБЫ СДЕЛАТЬ) Восстанавливает атрибут AD UserCertificate из файла сертификата

### <a name="syntax"></a>Синтаксис

```
Restore-ADSyncToolsExpiredCertificates
```

### <a name="description"></a>Описание
Подробное описание

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>Пример 2
```
Another example of how to use this cmdlet
```

## <a name="set-adsynctoolsconsistencyguid"></a>Set-ADSyncToolsConsistencyGuid

### <a name="synopsis"></a>Краткий обзор
Задает mS-Ds-ConsistencyGuid для пользователя AD.

### <a name="syntax"></a>Синтаксис

```
Set-ADSyncToolsConsistencyGuid [-User] <Object> [-Value] <Object> [<CommonParameters>]
```

### <a name="description"></a>Описание
Задание значения в атрибуте mS-Ds-ConsistencyGuid для целевого пользователя AD.

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>Пример 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>Параметры

#### <a name="-user"></a>-User
Целевой пользователь в AD для настройки ConsistencyGuid.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-value"></a>-Value
ImmutableId (массив байтов, GUID, строка GUID или строка в формате Base64).

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="test-adsyncnetworkport"></a>Test-ADSyncNetworkPort

### <a name="synopsis"></a>Краткий обзор
{{Введите краткий обзор}}

### <a name="syntax"></a>Синтаксис

```
Test-ADSyncNetworkPort [[-hostName] <String>] [[-port] <String>]
```

### <a name="description"></a>Описание
{{Введите описание}}

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Здесь добавьте пример описания }}

### <a name="parameters"></a>Параметры

#### <a name="-hostname"></a>-hostName
{{Введите описание имени узла}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-port
{{Введите описание порта}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport

### <a name="synopsis"></a>Краткий обзор
Создает файл трассировки и этап импорта AD.

### <a name="syntax"></a>Синтаксис

```
Trace-ADSyncToolsADImport [[-ADConnectorXML] <String>] [[-dc] <String>] [[-rootDN] <String>]
 [[-filter] <String>] [-SkipCredentials] [[-ADwatermark] <String>] [<CommonParameters>]
```

### <a name="description"></a>Описание
Создает трассировки всех запросов LDAP, выполненных при запуске импорта AD для AAD Connect, с заданной контрольной точки с водяным знаком AD (файлом cookie секции). Создает файл трассировки .\ADimportTrace_yyyyMMddHHmmss.log в текущей папке.

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>Пример 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>Параметры

#### <a name="-adconnectorxml"></a>-ADConnectorXML
{{Введите описание ADConnectorXML}}

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dc"></a>-dc
XML-файл экспорта для соединителя AD.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: DC1.domain.contoso.com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-rootdn"></a>-rootDN
Целевой контроллер домена.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: DC=Domain,DC=Contoso,DC=com
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-filter
Различающееся имя корня леса.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipcredentials"></a>-SkipCredentials
Типы объектов AD для трассировки, \> * означает все типы объектов.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-adwatermark"></a>-ADwatermark
При выполнении от имени администратора домена предоставить учетные данные AD не нужно.
Ввод водяного знака вручную вместо файла XML, например $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)".

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery

### <a name="synopsis"></a>Краткий обзор
Краткое описание

### <a name="syntax"></a>Синтаксис

```
Trace-ADSyncToolsLdapQuery [-Context] <String> [-Server] <String> [-Port] <Int32> [-Filter] <String>
 [<CommonParameters>]
```

### <a name="description"></a>Описание
Подробное описание

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Example of how to use this cmdlet
```

#### <a name="example-2"></a>Пример 2
```
Another example of how to use this cmdlet
```

### <a name="parameters"></a>Параметры

#### <a name="-context"></a>-Context
Описание param1.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-server"></a>-Server
Описание param2.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: Localhost
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-port"></a>-Port
Описание param2.

```yaml
Type: Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: 389
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-filter"></a>-Filter
Описание param2.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="update-adsynctoolsconsistencyguidmigration"></a>Update-ADSyncToolsConsistencyGuidMigration

### <a name="synopsis"></a>Краткий обзор
Обновляет пользователей с помощью нового значения ConsistencyGuid (ImmutableId).

### <a name="syntax"></a>Синтаксис

```
Update-ADSyncToolsConsistencyGuidMigration [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String>
 [-Action] <String> [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```

### <a name="description"></a>Описание
Обновляет пользователей с помощью нового значения ConsistencyGuid (ImmutableId), взятого из отчета ConsistencyGuid. Эта функция поддерживает параметр WhatIf. Примечание. Отчет ConsistencyGuid должен быть импортирован с разделителями-знаками табуляции.

### <a name="examples"></a>Примеры

#### <a name="example-1"></a>Пример 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2 -WhatIf
```

#### <a name="example-2"></a>Пример 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsConsistencyGuidMigration -Output .\AllSyncUsersTEST-Result2
```

### <a name="parameters"></a>Параметры

#### <a name="-distinguishedname"></a>-DistinguishedName
Различающееся имя.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-immutableidguid"></a>-ImmutableIdGUID
Значение ImmutableIdGUID.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-action"></a>-Action
Действие

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```

#### <a name="-output"></a>-Output
Имя для выходных LOG-файлов.

```yaml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-whatif"></a>-WhatIf
Показывает, что произойдет при запуске командлета.
Командлет не выполняется.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-confirm"></a>-Confirm
Запрос подтверждения перед выполнением командлета.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>Общие параметры
Этот командлет поддерживает общие параметры: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction и -WarningVariable.
Дополнительные сведения см. в разделе about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
