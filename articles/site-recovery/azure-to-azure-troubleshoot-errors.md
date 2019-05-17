---
title: Устранение ошибок и проблем с репликацией из Azure в Azure с помощью Azure Site Recovery | Документация Майкрософт
description: Устранение неполадок и ошибок при репликации виртуальных машин Azure для аварийного восстановления
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sujayt
ms.openlocfilehash: 3c87e159022b6dcf13daf2a2659c88c0529a8f48
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796427"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Устранение неполадок репликации виртуальных машин из Azure в Azure

В этой статье описаны распространенные проблемы в Azure Site Recovery, которые возникают при репликации и восстановлении виртуальных машин Azure из одного региона в другой, и способы их устранения. Дополнительные сведения о поддерживаемых конфигурациях см. в статье [Azure Site Recovery support matrix for replicating from Azure to Azure](site-recovery-support-matrix-azure-to-azure.md) (Матрица поддержки Azure Site Recovery для репликации виртуальных машин из Azure в Azure).

## <a name="list-of-errors"></a>Список ошибок
- **[Проблемы с квотами ресурсов Azure (код ошибки 150097)](#azure-resource-quota-issues-error-code-150097)**
- **[Доверенные корневые сертификаты (код ошибки 151066)](#trusted-root-certificates-error-code-151066)**
- **[Исходящие подключения для Site Recovery (код ошибки 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)**

## <a name="azure-resource-quota-issues-error-code-150097"></a>Проблемы с квотами ресурсов Azure (код ошибки 150097)
Чтобы создать виртуальные машины Azure в целевой области, которую планируется использовать в качестве региона аварийного восстановления, необходимо настроить подписку. Кроме того, подписка должна иметь достаточную квоту для создания виртуальных машин определенного размера. По умолчанию Site Recovery выбирает тот же размер для целевой виртуальной машины, что и для исходной. Если соответствующий размер не поддерживается, автоматически выбирается ближайший возможный. Если подходящий размер, поддерживающий конфигурацию исходной виртуальной машины, отсутствует, появится следующее сообщение об ошибке:

**Код ошибки** | **Возможные причины** | **Рекомендации**
--- | --- | ---
150097<br></br>**Сообщение**. Не удалось включить репликацию для виртуальной машины "Имя ВМ". | — Создание виртуальных машин в расположении целевой области может быть запрещено в идентификаторе подписки.</br></br>— Создание виртуальных машин определенных размеров в расположении целевой области может быть запрещено или не иметь достаточную квоту в идентификаторе подписки.</br></br>— Подходящий размер целевой виртуальной машины, соответствующий количеству сетевых адаптеров исходной виртуальной машины (2), не удалось найти для идентификатора подписки в расположении целевой области.| Сведения о том, как в подписке разрешить создание виртуальных машин необходимых размеров в целевом расположении, см. в статье [Запросы на увеличение квоты ядер Resource Manager](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). После включения повторите неудавшуюся операцию.

### <a name="fix-the-problem"></a>Устранение проблемы
Чтобы разрешить в подписке создание виртуальных машин необходимых размеров в целевом расположении, можно обратиться [в службу выставления счетов в Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Если целевое расположение имеет ограничение емкости, отключите репликацию и включите его в другом расположении, где подписка имеет достаточную квоту, чтобы создать виртуальные машины нужного размера.

## <a name="trusted-root-certificates-error-code-151066"></a>Доверенные корневые сертификаты (код ошибки 151066)

Если на виртуальной машине отсутствуют новейшие доверенные корневые сертификаты, включение репликации может завершиться ошибкой. Без сертификатов проверка подлинности и авторизация вызовов службы Site Recovery из виртуальной машины завершатся сбоем. Появится сообщение об ошибке для невыполненного задания включения репликации Site Recovery.

**Код ошибки** | **Возможная причина** | **рекомендации**;
--- | --- | ---
151066<br></br>**Сообщение**. Сбой при выполнении настройки Site Recovery. | Необходимые доверенные корневые сертификаты, используемые для авторизации и проверки подлинности, отсутствуют на компьютере. | — На виртуальной машине под управлением Windows должны быть доверенные корневые сертификаты. Дополнительные сведения см. в статье [Настройка доверенных корневых сертификатов и запрещенных сертификатов](https://technet.microsoft.com/library/dn265983.aspx).<br></br>— Для виртуальных машин под управлением Linux следуйте указаниям по доверенным корневым сертификатам, опубликованным распространителем версий операционной системы Linux.

### <a name="fix-the-problem"></a>Устранение проблемы
**Windows**

Установите все последние обновления Windows на виртуальной машине, чтобы на компьютере присутствовали все доверенные корневые сертификаты. При работе в отключенной среде следуйте стандартной процедуре обновления Windows в вашей организации, чтобы получить сертификаты. Если необходимые сертификаты отсутствуют на виртуальной машине, вызовы к службе Site Recovery завершатся ошибкой по соображениям безопасности.

Выполните стандартный для вашей организации процесс управления обновлениями Windows или обновлениями сертификатов, чтобы получить последние корневые сертификаты и обновленный список отзыва сертификатов на виртуальных машинах.

Чтобы убедиться, что проблема устранена, перейдите на веб-сайт login.microsoftonline.com из браузера на виртуальной машине.

**Linux**

Следуйте инструкциям вашего распространителя Linux, чтобы получить последние доверенные корневые сертификаты и обновленный список отзыва сертификатов на виртуальной машине.

Так как SuSE Linux использует символические ссылки, чтобы получить список сертификатов, сделайте следующее.

1.  Войдите как привилегированный пользователь.

2.  Запустите эту команду, чтобы изменить каталог.

      ``# cd /etc/ssl/certs``

1. Проверьте, есть ли на компьютере сертификат, выпущенный корневым центром сертификации Symantec.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Если такой сертификат не найден, чтобы скачать его, выполните следующую команду. Проверьте его на наличие ошибок, а затем выполните рекомендуемые действия для устранения сетевых сбоев.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Проверьте, есть ли на компьютере сертификат, выпущенный корневым центром сертификации Baltimore.

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Если такой сертификат не найден, скачайте его.  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. Проверьте, есть ли на компьютере сертификат DigiCert_Global_Root_CA.

    ``# ls DigiCert_Global_Root_CA.pem``

6. Если он не найден, выполните следующую команду, чтобы скачать его.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. Чтобы заменить хэши субъектов сертификата для скачанных сертификатов, запустите сценарий смены хэша.

    ``# c_rehash``

8.  Проверьте, были ли созданы для сертификатов хэши субъектов в виде символических ссылок.

    - Команда

      ``# ls -l | grep Baltimore``

    - Выход

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Команда

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Выход

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Команда

      ``# ls -l | grep DigiCert_Global_Root``

    - Выход

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  Создайте копию файла VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem и замените ее имя на b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. Создайте копию файла Baltimore_CyberTrust_Root.pem и замените ее имя на 653b494a.0

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Создайте копию файла DigiCert_Global_Root_CA.pem и замените ее имя на 3513523f.0

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Убедитесь в наличии файлов.  

    - Команда

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Выход

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Исходящие подключения для URL-адресов Site Recovery или IP-диапазонов (код ошибки 151037 или 151072)

Чтобы реплика Site Recovery заработала, от виртуальной машины требуется исходящее подключение для конкретного URL-адреса или IP-диапазонов. Если виртуальная машина находится за брандмауэром или использует правила группы безопасности сети (NSG) для управления исходящими подключениями, могут возникнуть следующие проблемы.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Проблема 1. Не удалось зарегистрировать виртуальную машину Azure в Site Recovery (151195) </br>
- **Возможная причина** </br>
  - Не удается подключиться к конечным точкам восстановления сайта из-за сбоя разрешения DNS.
  - Это чаще наблюдается во время повторной защиты, когда вы выполнили отработку отказа виртуальной машины, но DNS-сервер недоступен из региона аварийного восстановления.

- **Способы устранения:**
   - Если вы используете настраиваемый DNS-сервер, обеспечьте его доступность из региона аварийного восстановления. Чтобы проверить, есть ли у вас настраиваемый DNS-сервер, выберите "Виртуальная машина", затем щелкните сеть аварийного восстановления и выберите "DNS-серверы". Попробуйте получить доступ к DNS-серверу с виртуальной машины. Если он недоступен, обеспечьте его доступность, выполнив отработку отказа DNS-сервера или обеспечив видимость между сетью аварийного восстановления и DNS-сервером.

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Проблема 2. Сбой при выполнении настройки Site Recovery (151196)
- **Возможная причина** </br>
  - Не удается подключиться к конечным точкам IP4 удостоверения и проверки подлинности Office 365.

- **Способы устранения:**
  - Обеспечьте доступ Azure Site Recovery для выполнения проверки подлинности диапазонов IP-адресов Office 365.
    Если вы используете правила группы безопасности сети Azure (NSG) или прокси-сервер брандмауэра для управления исходящим сетевым подключением на виртуальной машине, разрешите обмен данными с диапазонами IP-адресов Office 365. Создайте [тег службы AAD](../virtual-network/security-overview.md#service-tags) на основе правила NSG, чтобы разрешить доступ ко всем IP-адресам, соответствующим AAD.
      - При добавлении новых адресов в AAD необходимо создать новые правила NSG.

> [!NOTE]
> Если виртуальные машины размещены за **стандартный** внутренней подсистемы балансировки нагрузки, то оно не будет доступа к IP-адресов Office 365 т. е. Login.micorsoftonline.com по умолчанию. Либо измените его, чтобы **основные** внутренний тип подсистемы балансировки нагрузки или создайте out привязку, как упоминалось в [статье](https://aka.ms/lboutboundrulescli).

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Проблема 3. Сбой при выполнении настройки Site Recovery (151197)
- **Возможная причина** </br>
  - Не удается подключиться к конечным точкам службы Azure Site Recovery.

- **Способы устранения:**
  - Обеспечение доступа Azure Site Recovery к [диапазонам IP-адресов Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) зависит от региона. Обеспечьте доступ к нужным диапазонам IP-адресов с виртуальной машины.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Проблема 4. Не удалось выполнить репликацию A2A, когда сетевой трафик проходит через прокси-сервера с локальным сервером (151072)
- **Возможная причина** </br>
  - Пользовательские настройки прокси-сервера недействительны, а агент службы Mobility Service ASR не смог автоматически определить параметры прокси-сервера из Internet Explorer


- **Способы устранения:**
  1. Агент службы Mobility Service обнаруживает настройки прокси-сервера из Internet Explorer в ОС Windows и в каталоге /etc/environment в ОС Linux.
  2. Если вы предпочитаете устанавливать прокси-сервер только для службы Mobility Service ASR, данные прокси-сервера можно внести в файл ProxyInfo.conf, расположенный по адресу:</br>
     - ``/usr/local/InMage/config/`` в ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` в ***Windows***
  3. Файл ProxyInfo.conf должен содержать параметры прокси-сервера в таком формате INI.</br>
                *[proxy]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Агент службы Mobility Service ASR поддерживает только ***прокси-серверы, которые не прошли проверку подлинности***.


### <a name="fix-the-problem"></a>Устранение проблемы
Чтобы внести [необходимые URL-адреса](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) или [диапазоны IP-адресов](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) в список разрешений, следуйте указаниям в [документации по настройке сети](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>На компьютере не найден диск (код ошибки 150039)

Необходимо инициализировать новый диск, подключенный к виртуальной машине.

**Код ошибки** | **Возможные причины** | **рекомендации**;
--- | --- | ---
150039<br></br>**Сообщение**. Диск данных Azure (имя_диска) (URI_диска) с логическим номером устройства (LUN) (значение_LUN) не был сопоставлен с соответствующим диском из виртуальной машины, о котором сообщается, что он имеет то же значение LUN. | Новый диск с данными был подключен к виртуальной машине, но не был инициализирован.</br></br>Диск с данными в виртуальной машине неправильно сообщает значение LUN, с использованием которого он был подключен к виртуальной машине.| Убедитесь, что диски с данными инициализированы, а затем повторите операцию.</br></br>Для Windows: [Подключение управляемого диска данных к виртуальной машине Windows с помощью портала Azure](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Для Linux: [Добавление диска к виртуальной машине Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>Устранение проблемы
Убедитесь, что диски с данными инициализированы, а затем повторите операцию.

- Для Windows: [Подключение управляемого диска данных к виртуальной машине Windows с помощью портала Azure](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Для Linux: [добавьте новый диск данных в Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Если проблема не исчезнет, обратитесь в службу поддержки.

## <a name="one-or-more-disks-are-available-for-protectionerror-code-153039"></a>Один или несколько дисков доступны для защиты (код ошибки 153039)
- **Возможная причина** </br>
  - Если один или несколько дисков были недавно добавлены к виртуальной машине после установки защиты. 
  - Если один или несколько дисков были инициализированы позже, после защиты виртуальной машины.

### <a name="fix-the-problem"></a>Устранение проблемы
Вы можете либо для защиты дисков или пропустить это предупреждение, чтобы вновь сделать работоспособное состояние репликации виртуальной машины.</br>
1. Для защиты дисков. Перейдите к реплицированные элементы > виртуальной Машины > диски > нажмите кнопку на незащищенном диске > включить репликацию.
 ![add_disks](./media/azure-to-azure-troubleshoot-errors/add-disk.png)
2. Чтобы отклонить предупреждение. Перейдите к реплицированные элементы > виртуальной Машины > Щелкните Закрыть оповещение, в разделе "Обзор".
![dismiss_warning](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)
## <a name="unable-to-see-the-azure-vm-or-resource-group--for-selection-in-enable-replication"></a>Не удалось открыть группу ресурсов или виртуальной Машины Azure для выбора в «включить репликацию»

 **Причина 1.  Группа ресурсов и исходная виртуальная машина находятся в разных расположениях** <br>
Azure Site Recovery в настоящее время предписаний, которые являются источниками область группы ресурсов и виртуальных машин должен находиться в том же расположении. Если это не так, то при применении защиты эту виртуальную машину не удастся обнаружить. Чтобы избежать этого можно включить репликацию из виртуальной Машины, а не в хранилище служб восстановления. Перейдите к виртуальной Машине Sourece > Свойства > аварийное восстановление и включить репликацию.

**Причина 2. Группа ресурсов не находится в выбранной подписке** <br>
Вы не сможете найти группу ресурсов во время применения защиты, если она не является частью данной подписки. Убедитесь, что группе ресурсов входит в выбранную подписку.

 **Причина 3. Устаревшая конфигурация** <br>
Причиной того, что виртуальная машина, которую нужно включить для репликации, не отображается, может быть устаревшая конфигурация Site Recovery на виртуальной машине Azure. Устаревшая конфигурация может остаться на виртуальной машине Azure в следующих случаях:

- Вы включили репликацию для виртуальной машины Azure с помощью Site Recovery, а затем удалили хранилище Site Recovery без явного отключения репликации на виртуальной машине.
- Вы включили репликацию для виртуальной машины Azure с помощью Site Recovery, а затем удалили группу ресурсов, содержащую хранилище Site Recovery, без явного отключения репликации на виртуальной машине.

### <a name="fix-the-problem"></a>Устранение проблемы

>[!NOTE]
>
>Обновите модуль AzureRM.Resources перед использованием упомянутого ниже скрипта.

Вы можете использовать [скрипт для удаления устаревшей конфигурации ASR](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1) и удалить устаревшую конфигурацию Site Recovery на виртуальной машине Azure. После удаления устаревшей конфигурации вы должны увидеть виртуальную машину.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Не удалось выбрать виртуальную машину для защиты
 **Причина 1.  На виртуальной машине установлены расширения, в которых произошел сбой или которые не отвечают на запросы** <br>
 Выберите "Виртуальные машины" > "Параметры" > "Расширения" и проверьте наличие расширений в состоянии сбоя. Удалите расширение в состоянии сбоя и повторите попытку применить защиту к виртуальной машине.<br>
 **Причина 2.  [Недопустимое состояние подготовки виртуальной машины](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>Недопустимое состояние подготовки виртуальной машины (код ошибки 150019)

Чтобы включить репликацию на виртуальной машине, должно отображаться состояние подготовки **Успешно**. Чтобы проверить состояние виртуальной машины, выполните следующие действия.

1.  На портале Azure в разделе **Все службы** выберите **Обозреватель ресурсов**.
2.  Разверните список **Подписки** и выберите нужную подписку.
3.  Разверните список **ResourceGroups** и выберите группу ресурсов, к которой относится виртуальная машина.
4.  Разверните список **ресурсов** и выберите виртуальную машину.
5.  Проверьте значение в поле **provisioningState** в представлении экземпляра справа.

### <a name="fix-the-problem"></a>Устранение проблемы

- Если в поле **provisioningState** отображается значение **Сбой**, обратитесь в службу поддержки и предоставьте необходимые сведения для устранения проблемы.
- Если в поле **provisioningState** отображается значение **Обновление**, возможно, началось развертывание другого расширения. Проверьте, выполняются ли на виртуальной машине какие-либо операции, дождитесь их завершения и еще раз попытайтесь выполнить задание **Включить репликацию**.

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>Невозможно выбрать целевую виртуальную сеть. Вкладка выбора сети неактивна.

**Причина 1. Если виртуальная машина подключена к сети, которая уже сопоставлена с целевой сетью.**
- Если исходная виртуальная машина является частью виртуальной сети, а другая виртуальная машина из той же виртуальной сети уже сопоставлена с сетью в целевой группе ресурсов, то выбор сети из раскрывающегося списка будет отключен по умолчанию.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**Причина 2. Если в виртуальной машине, которая ранее была защищена с помощью Azure Site Recovery, была отключена репликация.**
 - При отключении репликации виртуальной машины сетевое сопоставление не удаляется. Его необходимо удалить из хранилища службы восстановления, где находилась защищенная виртуальная машина. </br>
 Перейдите в Хранилище служб восстановления > Инфраструктура Site Recovery > Сетевое сопоставление. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - Целевая сеть, настроенная во время установки аварийного восстановления, может быть изменена после первоначальной настройки защиты виртуальной машины. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Обратите внимание, что изменение сетевого отображения влияет на все защищенные виртуальные машины, которые используют указанное сетевое сопоставление.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>Ошибка модели COM+ или службы теневого копирования томов (код ошибки — 151025)

**Код ошибки** | **Возможные причины** | **рекомендации**;
--- | --- | ---
151025<br></br>**Сообщение**. Не удалось установить расширение Site Recovery. | - Служба системных приложений COM+ отключена.</br></br>- Отключена служба теневого копирования томов (VSS).| Настройте службу системных приложений COM+ и службу теневого копирования томов для автоматического режима запуска или запуска вручную.

### <a name="fix-the-problem"></a>Устранение проблемы

Вы можете открыть консоль "Службы" и проверить, не задано ли для типа запуска службы системных приложений COM+ и службы теневого копирования томов значение "Отключено".
  ![com-error](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Неподдерживаемый размер управляемого диска (код ошибки 150172)


**Код ошибки** | **Возможные причины** | **рекомендации**;
--- | --- | ---
150172<br></br>**Сообщение**. Не удалось включить защиту для виртуальной машины, так как ее диск (имя_диска) имеет размер (размер_диска), который меньше минимального поддерживаемого размера, равного 1024 МБ. | Размер диска меньше поддерживаемого размера, равного 1024 МБ| Убедитесь, что размеры дисков находятся в диапазоне поддерживаемых размеров, и повторите операцию.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Включение защити завершилось сбоем, поскольку имя устройства, указанное в конфигурации GRUB вместо UUID (код ошибки 151126)

**Возможная причина:** </br>
Файлы конфигурации GRUB ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" или "/ etc / default / grub") могут содержать значение для параметров **root** и **resume** в качестве фактических имен устройств вместо UUID. Site Recovery требует наличие подхода UUID, поскольку имя устройства может меняться при перезагрузке виртуальной машины, а виртуальная машина может не выдавать одно и то же имя при сбое, что приводит к ошибкам. Например: </br>


- Следующая строка из файла GRUB **/boot/grub2/grub.cfg**. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- Следующая строка из файла GRUB **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Если вы заметили строки, выделенные полужирным шрифтом, GRUB содержит фактические имена устройств для параметров "root" и "resume" вместо UUID.

**Как исправить:**<br>
Имена устройств должны быть заменены на соответствующий UUID.<br>


1. Найти идентификатор UUID устройства с помощью команды «blkid \<имя устройства >». Например:<br>
   ```
   blkid /dev/sda1
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br>
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```<br>



1. Now replace the device name with its UUID in the format like "root=UUID=\<UUID>". For example, if we replace the device names with UUID for root and resume parameter mentioned above in the files "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub: then the lines in the files looks like. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
1. Restart the protection again

## Enable protection failed as device mentioned in the GRUB configuration doesn't exist(error code 151124)
**Possible Cause:** </br>
The GRUB configuration files ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub") may contain the parameters "rd.lvm.lv" or "rd_LVM_LV" to indicate the LVM device that should be discovered at the time of booting. If these LVM devices doesn't exist, then the protected system itself will not boot and stuck in the boot process. Even the same will be observed with the failover VM. Below are few examples:

Few examples: </br>

1. The following line is from the GRUB file **"/boot/grub2/grub.cfg"** on RHEL7. </br>
   *linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US.UTF-8*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".
1. The following line is from the GRUB file **"/etc/default/grub"** on RHEL7 </br>
   *GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".
1. The following line is from the GRUB file **"/boot/grub/menu.lst"** on RHEL6 </br>
   *kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet* </br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".<br>

**How to Fix:**<br>

If the LVM device doesn't exist, fix either by creating it or remove the parameter for the same from the GRUB configuration files and then retry the enable protection. </br>

## Site recovery mobility service update completed with warnings ( error code 151083)
Site Recovery mobility service has many components, one of which is called filter driver. Filter driver gets loaded into system memory only at a time of system reboot. Whenever there are  site recovery mobility service updates that has filter driver changes, we update the machine but still gives you warning that some fixes require a reboot. It means that the filter driver fixes can only be realized when a new filter driver is loaded which can happen only at the time of system reboot.<br>
**Please note** that this is just a warning and existing replication keeps on working even after the new agent update. You can choose to reboot anytime you want to get the benefits of new filter driver but if you don't reboot than also old filter driver keeps on working. Apart from filter driver, **benefits of  any other enhancements and fixes in mobility service get realized without any reboot when the agent gets updated.**  


## Protection couldn't be enabled as replica managed disk 'diskname-replica' already exists without expected tags in the target resource group( error code 150161

**Cause**: It can occur if the  virtual machine was protected earlier in the past and during disabling the replication, replica disk was not cleaned due to some reason.</br>
**How to fix:**
Delete the mentioned replica disk in the error message and restart the failed protection job again.

## Next steps
[Replicate Azure virtual machines](site-recovery-replicate-azure-to-azure.md)
