---
title: Краткое руководство. Создание виртуальной машины VMware в частном облаке
description: Описание процесса создания виртуальной машины VMware в Клаудсимпле частном облаке
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8d4712ca57801c15510ffcaf54852ce9287d343b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972398"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Создание виртуальных машин VMware в частном облаке

Чтобы создать виртуальные машины в частном облаке, начните с доступа к порталу Клаудсимпле из портал Azure.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Доступ к порталу CloudSimple

1. Выбор пункта **Все службы**.
2. Выполните поиск по запросу **Клаудсимпле Services**.
3. Выберите службу Клаудсимпле, для которой нужно создать частное облако.
4. На странице **Обзор** щелкните **Перейти на портал клаудсимпле** , чтобы открыть новую вкладку браузера для клаудсимпле Portal.  При появлении запроса войдите с помощью учетных данных для входа в Azure.  

    ![Запуск портала Клаудсимпле](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Запуск vCenter Web-UI

Теперь можно запустить vCenter, чтобы настроить виртуальные машины и политики.

Чтобы получить доступ к vCenter, запустите портал Клаудсимпле. На домашней странице в разделе **Общие задачи**щелкните **запустить клиент vSphere**.  Выберите частное облако и щелкните **запустить клиент vSphere** в частном облаке.

   ![Запуск клиента vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Отправка шаблона ISO или vSphere

  > [!WARNING]
  > Для передачи ISO используйте клиент vSphere HTML5.  Использование Flash Client может привести к ошибке.

1. Получите шаблон ISO или vSphere, который вы хотите передать в vCenter, чтобы создать виртуальную машину и сделать ее доступной в локальной системе.
2. В vCenter щелкните значок **диска** и выберите **всандатасторе**. Щелкните **файлы** , а затем — **создать папку**.
    ![vCenter ISO](media/vciso00.png)

3. Создайте папку с шаблоном "ISO and Templates".

4. Перейдите в папку ISO в ISO и Templates и щелкните **upload Files (отправить файлы**). Следуйте инструкциям на экране, чтобы отправить ISO-файл.

## <a name="create-a-virtual-machine-in-vcenter"></a>Создание виртуальной машины в vCenter

1. В vCenter щелкните значок **узлы и кластеры** .

2. Щелкните правой кнопкой мыши **рабочую нагрузку** и выберите **создать виртуальную машину**.
    ![Новая виртуальная машина](media/vcvm01.png)

3. Выберите **создать виртуальную машину** и нажмите кнопку **Далее**.
    ![Новая виртуальная машина](media/vcvm02.png)

4. Присвойте компьютеру имя, выберите расположение **виртуальной машины рабочей нагрузки** и нажмите кнопку **Далее**.
    ![Новая виртуальная машина](media/vcvm03.png)

5. Выберите ресурс вычислений **рабочей нагрузки** и нажмите кнопку **Далее**.
    ![Новая виртуальная машина](media/vcvm04.png)

6. Выберите **всандатасторе** и нажмите кнопку **Далее**.
    ![Новая виртуальная машина](media/vcvm05.png)

7. Выберите вариант совместимость ESXi 6,5 по умолчанию и нажмите кнопку **Далее**.
    ![Новая виртуальная машина](media/vcvm06.png)

8. Выберите гостевую ОС ISO для создаваемой виртуальной машины и нажмите кнопку **Далее**.
    ![Новая виртуальная машина](media/vcvm07.png)

9. Выберите параметры жесткого диска и сети. Для нового дисковода CD/DVD выберите **ISO-файл хранилища данных**.  Если вы хотите разрешить трафик с общедоступного IP-адреса на эту виртуальную машину, выберите сеть в качестве **ВМ-1**.
    ![Новая виртуальная машина](media/vcvm08.png)

10. Откроется окно выбора. Выберите файл, который вы ранее перегрузили в папку ISO и Templates, и нажмите кнопку **ОК**.
    ![Новая виртуальная машина](media/vcvm10.png)

11. Проверьте параметры и нажмите кнопку **ОК** , чтобы создать виртуальную машину.
    ![Новая виртуальная машина](media/vcvm11.png)

Теперь виртуальная машина добавляется в ресурсы для загрузки рабочей нагрузки и готова к использованию. 
![Новая виртуальная машина](media/vcvm12.png)

Теперь базовая настройка завершена. Вы можете начать использовать частное облако так же, как и локальную инфраструктуру виртуальных машин.

В следующих разделах содержатся дополнительные сведения о настройке DNS и DHCP для рабочих нагрузок частного облака и изменении конфигурации сети по умолчанию.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Добавление пользователей и источников удостоверений в vCenter (необязательно)

Клаудсимпле назначает учетную запись пользователя vCenter по умолчанию `cloudowner@cloudsimple.local`с именем пользователя. Чтобы приступить к работе, дополнительная настройка учетных записей не требуется.  Клаудсимпле обычно назначает администраторам права, необходимые для выполнения обычных операций.  Настройте локальную службу Active Directory или Azure AD в качестве [дополнительного источника удостоверений](set-vcenter-identity.md) в частном облаке.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Создание DNS-и DHCP-сервера (необязательно)

Приложениям и рабочим нагрузкам, выполняемым в среде частного облака, требуется разрешение имен и службы DHCP для назначения адресов и поиска по IP-адресам. Для предоставления этих служб требуется правильная инфраструктура DHCP и DNS. Вы можете настроить виртуальную машину в vCenter, чтобы предоставить эти службы в среде частного облака.

предварительные требования

* Группа распределенных портов с настроенной виртуальной ЛС

* Настройка маршрута к локальным или DNS-серверам в Интернете

* Шаблон виртуальной машины или ISO-образ для создания виртуальной машины

Следующие ссылки содержат рекомендации по настройке DHCP-и DNS-серверов в Linux и Windows.

#### <a name="linux-based-dns-server-setup"></a>Установка DNS-сервера на основе Linux

Linux предлагает различные пакеты для настройки DNS-серверов.  Ниже приведена ссылка на инструкции по настройке DNS-сервера BIND с открытым исходным кодом.

[Пример установки](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Установка на основе Windows

В этих разделах Майкрософт описывается настройка сервера Windows Server в качестве DNS-сервера и в качестве DHCP-сервера.

[Windows Server как DNS-сервер](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server как DHCP-сервер](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Настройка конфигурации сети (необязательно)

Сетевые страницы на портале Клаудсимпле позволяют указать конфигурацию для таблиц брандмауэра и общедоступных IP-адресов для виртуальных машин.

### <a name="allocate-public-ips"></a>Выделение общедоступных IP-адресов

1. Перейдите в раздел **сетевое > общедоступный IP-адрес** на портале клаудсимпле.
2. Щелкните **выделить общедоступный IP-адрес**.
3. Введите имя для указания записи IP-адреса.
4. Используйте расположение по умолчанию.
5. При необходимости используйте ползунок, чтобы изменить время ожидания простоя.
6. Введите локальный IP-адрес, для которого требуется назначить общедоступный IP-адрес.
7. При необходимости введите соответствующее DNS-имя.
8. Нажмите кнопку **Done**(Готово).

    ![Общедоступный IP-адрес](media/quick-create-pc-public-ip.png)

Начинается задача выделения общедоступного IP-адреса. Состояние задачи можно проверить на странице **действия > задачи** . После завершения выделения новая запись отображается на странице общедоступные IP-адреса.

Виртуальная машина, с которой должен быть сопоставлен этот IP-адрес, должна быть настроена с использованием локального адреса, указанного выше. Процедура настройки IP-адреса зависит от операционной системы виртуальной машины. Для правильной процедуры обратитесь к документации по операционной системе виртуальной машины.

#### <a name="example"></a>Пример

Например, ниже приведены сведения для Ubuntu 16,04.

Добавьте статический метод в конфигурацию семейства адресов INET в файле/ЕТК/Нетворк/интерфацес. Измените адрес, сетевую маску и значения шлюза. В этом примере мы используем интерфейс eth0, внутренний IP-адрес 192.168.24.10, адрес шлюза 192.168.24.1 и маска сети 255.255.255.0. В вашей среде сведения о доступных подсетях предоставляются в приветственном сообщении.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Отключите интерфейс вручную.

```
sudo ifdown eth0
```
Вручную Включите интерфейс.

```
sudo ifup eth0
```

По умолчанию весь входящий трафик из Интернета отклоняется. Если вы хотите открыть любой другой порт, создайте [таблицу брандмауэра](firewall.md).

После настройки внутреннего IP-адреса в качестве статического IP-адреса убедитесь, что к Интернету можно подключиться из виртуальной машины.

```
ping 8.8.8.8
```
Также убедитесь, что вы можете подключиться к виртуальной машине из Интернета, используя общедоступный IP-адрес.

Убедитесь, что все правила иптабле на виртуальной машине не блокируют порт 80.
        
```
netstat -an | grep 80
```

Запустите HTTP-сервер, который прослушивает порт 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

или диспетчер конфигурации служб

```
python3 -m http.server 80
```
Запустите браузер на рабочем столе и наведите его на порт 80 для общедоступного IP-адреса, чтобы просмотреть файлы на виртуальной машине.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Правила брандмауэра Клаудсимпле по умолчанию для общедоступного IP-адреса

* Трафик VPN: Разрешен весь трафик между (от или до) VPN и всеми сетями рабочей нагрузки и сетью управления.
* Внутренний трафик частного облака: Разрешен весь трафик между сетями рабочей нагрузки (от/до) и сетью управления (показанным выше).
* Интернет — трафик:
  * Весь входящий трафик из Интернета отклоняется для сетей рабочей нагрузки и сети управления.
  * Разрешен весь исходящий трафик к Интернету из сетей рабочей нагрузки или сети управления.

Можно также изменить способ защиты трафика с помощью функции правила брандмауэра. Дополнительные сведения см. в разделе [Настройка таблиц и правил брандмауэра](firewall.md).

## <a name="install-solutions-optional"></a>Установка решений (необязательно)

Вы можете установить решения в частном облаке Клаудсимпле, чтобы воспользоваться всеми преимуществами среды для работы с частным облаком. Для защиты виртуальных машин можно настроить резервное копирование, аварийное восстановление, репликацию и другие функции. К примерам относятся VMware Site Recovery Manager (VMware СРМ) и репликация Veeam Backup &.

Чтобы установить решение, необходимо запросить дополнительные привилегии в течение ограниченного периода. См. раздел [эскалация привилегий](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Следующие шаги

* [Использование виртуальных машин VMware в Azure](quickstart-create-vmware-virtual-machine.md)
* [Подключение к локальной сети с помощью Azure ExpressRoute](on-premises-connection.md)
* [Настройка VPN-шлюзов в сети Клаудсимпле](vpn-gateway.md)
