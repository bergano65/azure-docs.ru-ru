---
title: Создание ключей, защищенных аппаратным модулем безопасности, и их передача в Azure Key Vault | Документация Майкрософт
description: Данная статья поможет вам подготовить и создать ваши собственные ключи, защищенные аппаратным модулем безопасности, а затем передать их в хранилище ключей Azure. Также известные как собственные ключи или BYOK.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 28f066668d580f16d831371f2d02a5abcc0e84b3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429737"
---
# <a name="import-hsm-protected-keys-for-key-vault-legacy"></a>Импортные HSM-защищенные ключи для Key Vault (наследие)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Чтобы обеспечить более высокий уровень защиты при работе с хранилищем ключей Azure, можно импортировать ключи или создать их в аппаратных модулях безопасности (ключи никогда не покидают их пределы). Сценарий с использованием собственного ключа клиента называется *BYOK*. Azure Key Vault использует семейство HSMs nCipher nShield (FIPS 140-2 Уровень 2 проверено) для защиты ваших ключей.

Информация в этой статье поможет вам подготовить и создать ваши собственные ключи, защищенные аппаратным модулем безопасности, а затем передать их в хранилище ключей Azure.

Эта функция недоступна для Китая.

> [!NOTE]
> Для получения дополнительной информации о [What is Azure Key Vault?](../general/overview.md) Убежище ключей Azure см.  
> Указания по началу работы, включая создание хранилища для ключей, защищенных модулем HSM, см. в [этой статье](../general/overview.md).

Дополнительные сведения о создании и передаче ключей, защищенных аппаратным модулем безопасности, через Интернет:

* Ключ создается на автономной рабочей станции, что позволяет уменьшить область атаки.
* Ключ шифруется с использованием ключа обмена ключами и остается зашифрованным до тех пор, пока не будет передан в аппаратные модули безопасности хранилища ключей Azure. Исходную рабочую станцию покидает только зашифрованная версия ключа.
* Набор инструментов задает свойства ключа клиента, который привязывает ваш ключ к системе безопасности хранилища ключей Azure. В связи с этим после, как только аппаратные модули безопасности хранилища ключей Azure получат и расшифруют ваш ключ, использовать его смогут только они. Экспортировать ключ нельзя. Эта связывание обеспечивается HSM nCipher.
* Ключ обмена ключами, используемый для шифрования вашего ключа, создается в аппаратных модулях безопасности хранилища ключей Azure и не может быть экспортирован. Аппаратные модули безопасности не допускают существования ключа обмена ключами в чистом виде за пределами аппаратных модулей безопасности. Кроме того, набор инструментов включает в себя подтверждение от nCipher, что KEK не является экспортируемым и был создан внутри подлинного HSM, который был изготовлен nCipher.
* Набор инструментов включает в себя подтверждение от nCipher о том, что мир безопасности Azure Key Vault также был создан на подлинном HSM, изготовленном nCipher. Эта аттестация является доказательством того, что корпорация Майкрософт использует подлинное оборудование.
* Корпорация Майкрософт применяет отдельные ключи шифрования ключей, а также отдельные системы безопасности в каждом географическом регионе. Такое разделение значит, что ваш ключ может использоваться только в центрах обработки данных того региона, в котором он был зашифрован. Например, ключ европейского клиента нельзя использовать в центрах обработки данных в Северной Америке или Азии.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Дополнительная информация о HSMs nCipher и службах Майкрософт

nCipher Security, компания Entrust Datacard, является лидером на рынке HSM общего назначения, расширяя возможности ведущих мировых организаций, предоставляя доверие, целостность и контроль их бизнес-критической информации и приложений. Криптографические решения nCipher обеспечивают безопасность новых технологий – облачных технологий, IoT, блокчейна, цифровых платежей – и помогают выполнять новые требования по соблюдению требований, используя ту же проверенную технологию, от которой сегодня зависят глобальные организации для защиты от угроз их конфиденциальным данным, сетевым коммуникациям и корпоративной инфраструктуре. nCipher обеспечивает доверие для критически важных для бизнеса приложений, обеспечивая целостность данных и поставив клиентов в полный контроль - сегодня, завтра, во все времена.

Корпорация Майкрософт сотрудничала с nCipher Security для улучшения современного качества HSMs. Эти улучшения позволят вам пользоваться стандартными преимуществами размещаемых служб, не теряя контроль над ключами, а корпорации Майкрософт — управлять аппаратными модулями безопасности за вас. Как облачный сервис, Azure Key Vault масштабируется без предварительного уведомления, чтобы удовлетворить пики использования вашей организации. В то же время ваш ключ защищен внутри HSMs корпорации Майкрософт: вы сохраняете контроль над ключевым жизненным циклом, поскольку вы генерируете ключ и передаете его в HSMs корпорации Майкрософт.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Реализация сценария BYOK для хранилища ключей Azure

Используйте сведения и процедуры этого раздела при создании собственного ключа, защищенного аппаратным модулем безопасности, и передачи этого ключа в хранилище ключей Azure — сценарий BYOK.

## <a name="prerequisites-for-byok"></a>Предварительные требования для BYOK

Список предварительных требований для реализации сценария BYOK для хранилища ключей Azure к см. в приведенной ниже таблице.

| Требование | Дополнительные сведения |
| --- | --- |
| Подписка на Azure |Для создания хранилища ключей Azure требуется подписка Azure: [зарегистрируйтесь для получения бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/) |
| Поддержка защищенных ключей аппаратных модулей безопасности в хранилище ключей Azure уровня служб "Премиум". |Дополнительные сведения об уровнях служб и возможностях хранилища ключей Azure см. на веб-сайте [Цены на хранилище ключей Azure](https://azure.microsoft.com/pricing/details/key-vault/). |
| nCipher nShield HSMs, смарт-карты и программное обеспечение поддержки |Вы должны иметь доступ к nCipher аппаратного модуля безопасности и основные оперативные знания nCipher nShield HSMs. Для списка совместимых моделей или для покупки HSM, если у вас его нет, смотрите [модуль безопасности оборудования nCipher nShield.](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) |
| Следующее оборудование и программное обеспечение:<ol><li>Офлайн x64 рабочая станция с минимальной системой работы Windows Windows 7 и nCipher nShield программного обеспечения, которое, по крайней мере версия 11.50.<br/><br/>Если рабочая станция выполняется на ОС Windows 7, [установите Microsoft .NET Framework 4.5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Рабочая станция, подключенная к Интернету, с операционной системой Windows версии не ниже Windows 7 и установленным [Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) **версии не ниже 1.1.0**.</li><li>USB-накопитель или другое переносное устройство хранения, на котором имеется не менее 16 МБ свободного места.</li></ol> |По соображениям безопасности рекомендуется не подключать первую рабочую станцию к сети. Однако это не применяется принудительно программным путем.<br/><br/>Далее эта рабочая станция называется отключенной рабочей станцией.</p></blockquote><br/>Кроме того, если ключ клиента предназначен для производственной сети, рекомендуется использовать вторую, отдельную рабочую станцию для скачивания набора средств и отправки ключа клиента. Но в целях тестирования можно использовать первую рабочую станцию.<br/><br/>Далее вторая рабочая станция называется рабочей станцией, подключенной к Интернету.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Создание и передача ключа в аппаратный модуль безопасности хранилища ключей Azure

Для создания и передачи ключа в аппаратный модуль безопасности хранилища ключей Azure используются следующие пять шагов:

* [Шаг 1. Подготовка рабочей станции, подключенной к Интернету](#step-1-prepare-your-internet-connected-workstation)
* [Шаг 2. Подготовка отключенной рабочей станции](#step-2-prepare-your-disconnected-workstation)
* [Шаг 3. Создание ключа](#step-3-generate-your-key)
* [Шаг 4. Подготовка ключа к передаче](#step-4-prepare-your-key-for-transfer)
* [Шаг 5. Передача ключа в хранилище ключей Azure](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Шаг 1. Подготовка рабочей станции, подключенной к Интернету

Чтобы подготовить рабочую станцию, подключенную к Интернету, выполните описанные ниже действия.

### <a name="step-11-install-azure-powershell"></a>Шаг 1.1. Установка Azure PowerShell

С компьютера, подключенного к Интернету, загрузите и установите модуль Azure PowerShell, который содержит командлеты для управления хранилищем ключей Azure. Инструкции по установке см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Шаг 1.2. Получение идентификатора подписки Azure

Запустите сеанс Azure PowerShell и выполните вход в учетную запись Azure с помощью следующей команды:

```Powershell
   Connect-AzAccount
```
Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. Затем воспользуйтесь командой [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

```powershell
   Get-AzSubscription
```
В выходных данных найдите идентификатор для подписки, которая будет использоваться для хранилища ключей Azure. Этот идентификатор подписки понадобится позже.

Не закрывайте окно Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Шаг 1.3. Загрузка набора инструментов BYOK для хранилища ключей Azure

Перейдите в Центр загрузки Майкрософт и [скачайте набор инструментов BYOK для хранилища ключей Azure](https://www.microsoft.com/download/details.aspx?id=45345) для своего географического региона или экземпляра Azure. Чтобы определить имя пакета для скачивания и его хэш SHA-256, используйте следующие сведения.

---
**США:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Европе:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Азии:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Латинская Америка:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Япония:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Корея**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**Южная Африка:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAAD896Da732e31433D14CB9FC83AC3c6776f46d98620784A

---
**Оаэ:**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B0662AA0913EA411DAB977929248C65F365FF953BB9F241D5FC0D0D3

---
**Австралия:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Лазурное правительство:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**Министерство обороны США**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Канада:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Германия:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Германия Публичная:**

KeyVault-BYOK-Tools-Германия-Public.zip

5453936D0A0C99C8117B724c34a5E50fd204CFCBD75C78972B7855664A29

---
**Индия:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Франция:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Великобритания:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Швейцария:**

KeyVault-BYOK-Tools-Switzerland.zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD959AD2FE9

---


Чтобы проверить целостность скачанного набора инструментов BYOK, в сеансе Azure PowerShell запустите командлет [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) .

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Набор инструментов включает в себя следующее.

* Пакет ключей обмена ключами с именем, начинающимся с **BYOK-KEK-pkg-.**
* Пакет Security World с именем, начинающимся с **BYOK-SecurityWorld-pkg-**
* Сценарий на Python с именем **verifykeypackage.py.**
* Исполняемый файл командной строки с именем **KeyTransferRemote.exe** и связанными DLL-файлами.
* Распространяемый пакет Visual C++ с именем **vcredist_x64.exe.**

Скопируйте пакет на USB-накопитель или другое переносное устройство.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Шаг 2. Подготовка отключенной рабочей станции

Чтобы подготовить рабочую станцию, не подключенную к сети (к Интернету или внутренней сети), выполните указанные ниже действия.

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Шаг 2.1: Подготовьте отключенную рабочую станцию с помощью nCipher nShield HSM

Установите программное обеспечение поддержки nCipher на компьютер ЕС, а затем прикрепите nCipher nShield HSM к этому компьютеру.

Убедитесь, что инструменты nCipher находятся на вашем пути **(%nfast_home%-бен**). Например, введите следующую команду:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Для получения дополнительной информации смотрите руководство пользователя, включенное в hSM nShield.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Шаг 2.2. Установка набора инструментов BYOK на отключенную рабочую станцию

Скопируйте пакет набора инструментов BYOK с USB-накопителя или другого переносного устройства, а затем выполните указанные ниже действия.

1. Извлеките файлы из загруженного пакета в любую папку.
2. Запустите файл vcredist_x64.exe из этой папки.
3. Следуйте инструкциям по установке компонентов среды выполнения Visual C++ для Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Шаг 3. Создание ключа

Чтобы создать свой ключ, выполните на отключенной рабочей станции указанные ниже действия. Для выполнения этого шага HSM должен находиться в режиме инициализации. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Шаг 3.1. Изменение режима HSM на I

Если вы используете nCipher nShield Edge, чтобы изменить режим: 1. Используйте кнопку "Режим", чтобы выделить необходимый режим. 2. Через несколько секунд нажмите и удерживайте пару секунд кнопку "Очистить". При изменении режима светодиод нового режима перестает мигать и остается освещенным. Индикатор состояния может беспорядочно мигать в течение нескольких секунд. Затем, когда устройство будет готово, он станет мигать регулярно. В противном случае устройство останется в текущем режиме. При этом загорится соответствующий индикатор режима.

### <a name="step-32-create-a-security-world"></a>Шаг 3.2. Создание системы безопасности

Запустите командный запрос и запустите программу nCipher new-world.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Эта программа создает файл **Security World** в каталоге %NFAST_KMDATA%\local\world, который соответствует папке C:\ProgramData\nCipher\Key Management Data\local. Для кворума можно использовать различные значения, но в нашем примере вам предлагается ввести три пустые карты и булавки для каждой из них. После этого любые две карты предоставят полный доступ к системе безопасности. Эти карты станут **набором карт администратора** для новой системы безопасности.

> [!NOTE]
> Если HSM не поддерживает более новый набор тестов DLf3072s256mRijndael, вы можете заменить --cipher-suite= DLf3072s256mRijndael на --cipher-suite=DLf1024s160mRijndael.
> 
> Мир безопасности, созданный с new-world.exe, который поставляется с версией программного обеспечения nCipher 12.50, не совместим с этой процедурой BYOK. Есть два варианта.
> 1) Понизите версию программного обеспечения nCipher до 12.40.2 для создания нового мира безопасности.
> 2) Обратитесь в службу поддержки nCipher и попросите предоставить hotfix для версии программного обеспечения 12.50, которая позволяет использовать версию 12.40.2 new-world.exe, совместимую с этой процедурой BYOK.

После этого выполните описанные ниже действия.

* Создайте резервную копию файла системы безопасности. Защитите файл системы безопасности, карты администратора и их закрепления и убедитесь, что все имеют доступ не больше, чем к одной карте.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Шаг 3.3. Изменение режима HSM на "O"

Если вы используете nCipher nShield Edge, чтобы изменить режим: 1. Используйте кнопку "Режим", чтобы выделить необходимый режим. 2. Через несколько секунд нажмите и удерживайте пару секунд кнопку "Очистить". При изменении режима светодиод нового режима перестает мигать и остается освещенным. Индикатор состояния может беспорядочно мигать в течение нескольких секунд. Затем, когда устройство будет готово, он станет мигать регулярно. В противном случае устройство останется в текущем режиме. При этом загорится соответствующий индикатор режима.

### <a name="step-34-validate-the-downloaded-package"></a>Шаг 3.4. Проверка скачанного пакета

Этот шаг необязателен, но рекомендуется, поскольку позволяет проверить следующее.

* Ключ обмена, который включен в набор инструментов, был создан из подлинного nCipher nShield HSM.
* Хэш мира безопасности, который включен в набор инструментов, был создан в подлинном nCipher nShield HSM.
* Ключ обмена ключами недоступен для экспорта.

> [!NOTE]
> Чтобы проверить загруженный пакет, HSM должен быть подключен, включен, и должен иметь мир безопасности на нем (например, тот, который вы только что создали).

Проверка загруженного пакета

1. Запустите сценарий verifykeypackage.py, указав одно из приведенных ниже значений в зависимости от вашего географического региона или экземпляра Azure.

   * Для Северной Америки:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Для Европы:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Для Азии:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Для Латинской Америки:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Для Японии:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Для Кореи:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Для южной Африки:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * Для ОАЭ:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * Для Австралии:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Для [Azure для государственных организаций](https://azure.microsoft.com/features/gov/), использующего экземпляр Azure для правительства США:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Для министерства обороны США:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Для Канады:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Для Германии:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Для Германии Общественность:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Для Индии:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Для Франции:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Для Соединенного Королевства:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
   * Для Швейцарии:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1

     > [!TIP]
     > Программное обеспечение nCipher nShield включает в себя питона на уровне % NFAST_HOME%
     >
     >
2. Должен появиться следующий текст, показывающий, что проверка пройдена: **Результат: УСПЕШНО**

Этот скрипт проверяет цепочку подписавшего до корневого ключа nShield. Хэш этого корневого ключа встроен в скрипт, и его значение должно быть **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Вы также можете подтвердить это значение отдельно, посетив [веб-сайт nCipher.](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation)

Теперь вы готовы создать новый ключ.

### <a name="step-35-create-a-new-key"></a>Шаг 3.5. Создание ключа

Создайте ключ с помощью программы nCipher nShield **generatekey.**

Для создания ключа выполните следующую команду:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

При выполнении команды следуйте приведенным ниже инструкциям.

* Параметру *protect* должно быть задано значение **module**, как показано. В результате будет создан ключ, защищенный модулем. Набор средств BYOK не поддерживает ключи, защищенные OCS.
* Замените значение *contosokey* на **ident**, а **plainname** — любым строковым значением. Чтобы минимизировать административные затраты и снизить риск возникновения ошибок, рекомендуется использовать для обоих параметров одно и то же значение. Значение **Ident** должно содержать только цифры, тире и буквы нижнего регистра.
* В данном примере значение параметра pubexp остается пустым (по умолчанию), но можно указать и конкретные значения. Для получения дополнительной [информации](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based) см.

Эта команда создает файл ключа с маркером в папке %NFAST_KMDATA%\local с именем, начинающимся с **key_simple_**, за которым следует значение параметра **ident**, указанное в команде. Пример: **key_simple_contosokey**. Этот файл содержит зашифрованный ключ.

Создайте резервную копию файла ключа с токеном в безопасном расположении.

> [!IMPORTANT]
> После передачи ключа в хранилище ключей Azure корпорация Майкрософт не сможет экспортировать ключ обратно, поэтому крайне важно сделать резервную копию ключа и системы безопасности. Свяжитесь с [nCipher](https://www.ncipher.com/about-us/contact-us) для получения рекомендаций и рекомендаций для резервного копирования ключа.
>


После этого ключ можно передать в хранилище ключей Azure.

## <a name="step-4-prepare-your-key-for-transfer"></a>Шаг 4. Подготовка ключа к передаче

Чтобы подготовить ключ к передаче, выполните на отключенной рабочей станции указанные ниже действия.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Шаг 4.1. Создание копии ключа с ограниченными разрешениями

Откройте новую командную строку и измените текущий каталог на каталог, в который вы разархивировали ZIP-файл BYOK. Чтобы ограничить разрешения в ключе, выполните через командную строку одну из следующих команд в зависимости от вашего географического региона или экземпляра Azure.

* Для Северной Америки:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Для Европы:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Для Азии:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Для Латинской Америки:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Для Японии:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Для Кореи:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Для южной Африки:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* Для ОАЭ:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* Для Австралии:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Для [Azure для государственных организаций](https://azure.microsoft.com/features/gov/), использующего экземпляр Azure для правительства США:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Для министерства обороны США:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Для Канады:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Для Германии:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Для Германии Общественность:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Для Индии:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Для Франции:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Для Соединенного Королевства:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* Для Швейцарии:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

При запуске этой команды замените *contosokey* тем же значением, которое вы указали при выполнении этапа **Шаг 3.5. Создание ключа** процедуры [Создание ключа](#step-3-generate-your-key) .

Появится запрос на подключение карт администратора системы безопасности.

Когда команда завершается, вы видите **Результат: УСПЕХ** и копия ключа с уменьшенными разрешениями находятся в файле, названном key_xferacId_\<> contosokey.

Вы можете проверить ACLS, используя следующие команды, используя утилиты nCipher nShield:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  При запуске этих команд замените contosokey тем же значением, которое вы указали на этапе **Шаг 3.5. Создание ключа** в разделе [Создание ключа](#step-3-generate-your-key).

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Шаг 4.2: Шифрование ключа с помощью ключа обмена ключом Майкрософт

Выполните одну из следующих команд в зависимости от вашего географического региона или экземпляра Azure:

* Для Северной Америки:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Европы:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Азии:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Латинской Америки:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Японии:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Кореи:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для южной Африки:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для ОАЭ:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Австралии:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для [Azure для государственных организаций](https://azure.microsoft.com/features/gov/), использующего экземпляр Azure для правительства США:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для министерства обороны США:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Канады:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Германии:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Германии Общественность:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Индии:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Франции:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Соединенного Королевства:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Швейцарии:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


При выполнении команды следуйте приведенным ниже инструкциям.

* Замените *contosokey* идентификатором, который вы использовали для создания ключа на этапе **Шаг 3.5. Создание ключа** в разделе [Создание ключа](#step-3-generate-your-key).
* Замените *SubscriptionID* на идентификатор подписки Azure, содержащей ваше хранилище ключей. Это значение было получено ранее на этапе **Шаг 1.2. Получение идентификатора подписки Azure** процедуры [Подготовка рабочей станции, подключенной к Интернету](#step-1-prepare-your-internet-connected-workstation) .
* Замените *ContosoFirstHSMKey* меткой, которая используется для имени выходного файла.

После выполнения команды отобразится сообщение **Результат: успешно**, а в текущей папке появится новый файл с именем KeyTransferPackage-*ContosoFirstHSMkey*.byok.

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Шаг 4.3. Копирование пакета передачи ключа на рабочую станцию, подключенную к Интернету

С помощью USB-накопителя или другого переносного устройства скопируйте выходной файл из предыдущего шага (KeyTransferPackage-ContosoFirstHSMkey.byok) на рабочую станцию, подключенную к Интернету.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Шаг 5. Передача ключа в хранилище ключей Azure

Наконец, на рабочей станции, подключенной к Интернету, выполните командлет [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey), чтобы отправить пакет передачи ключа, скопированный с отключенной рабочей станции, в аппаратный модуль безопасности Azure Key Vault.

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

При успешном завершении отправки на экране появятся свойства добавленного ключа.

## <a name="next-steps"></a>Дальнейшие действия

Теперь ключ, защищенный с помощью аппаратного модуля безопасности, можно использовать в хранилище ключей. Для получения дополнительной информации, [comparison](https://azure.microsoft.com/pricing/details/key-vault/)см.
