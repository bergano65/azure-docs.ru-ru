---
title: Развертывание СУБД на виртуальных машинах Azure в Oracle для рабочей нагрузки SAP | Документы Майкрософт
description: Развертывание СУБД на виртуальных машинах Azure в Oracle для рабочей нагрузки SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5182b621779cf31f3c7da99674ab24fe6efe702d
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850809"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Развертывание СУБД на Виртуальных машинах Azure для рабочей нагрузки SAP

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a 
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c 
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef 
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a 
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d 
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md


В этом документе рассматривается несколько аспектов, которые следует учитывать при развертывании Oracle Database для рабочей нагрузки SAP в Azure IaaS. Прежде чем приступить к изучению этой статьи, рекомендуется ознакомиться со статьей [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](dbms_guide_general.md). Мы также советуем прочитать остальные руководства в [документации по рабочей нагрузке SAP в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

В примечании к SAP [2039619] вы можете найти сведения о версиях Oracle и соответствующих версиях ОС, которые поддерживаются для работы SAP в Oracle в Azure.

Общие сведения о запуске SAP Business Suite в Oracle можно найти на странице о [SAP в Oracle](https://www.sap.com/community/topic/oracle.html).
Программное обеспечение Oracle поддерживает работу с Microsoft Azure. Чтобы ознакомиться с дополнительными сведениями об общей поддержке Windows Hyper-V и Azure, просмотрите [часто задаваемые вопросы по Oracle и Microsoft Azure](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>Примечания к SAP, относящиеся к Oracle, SAP и Azure 

Следующие примечания к SAP актуальны для развертывания SAP в Azure.

| Номер примечания | Название |
| --- | --- |
| [1928533] |Приложения SAP в Azure: поддерживаемые продукты и типы виртуальных машин Azure |
| [2015553] |SAP в Microsoft Azure: необходимые компоненты для поддержки |
| [1999351] |Устранение неполадок, связанных с расширенным мониторингом Azure для SAP |
| [2178632] |Ключевые метрики мониторинга для SAP в Microsoft Azure |
| [2191498] |SAP на платформе Linux в Azure: расширенный мониторинг |
| [2039619] |Приложения SAP в Microsoft Azure с использованием Oracle Database: поддерживаемые продукты и версии |
| [2243692] |Linux на виртуальной машине Microsoft Azure (IaaS): проблемы с лицензированием SAP |
| [2069760] |Установка и обновление Oracle Linux 7.x SAP |
| [1597355] |Рекомендация по области буфера для Linux |
| [2171857] |Oracle Database 12c: поддержка файловой системы в Linux |
| [1114181] |Oracle Database 11g: поддержка файловой системы в Linux |

Точные конфигурации и функциональные возможности, поддерживаемые Oracle и SAP в Azure, описаны в примечании к SAP [2039619](https://launchpad.support.sap.com/#/notes/2039619).

Windows и Oracle Linux являются единственными операционными системами, которые поддерживаются в Oracle и SAP в Azure. Широко используемые дистрибутивы SLES и RHEL Linux не поддерживаются для развертывания компонентов Oracle в Azure. Компоненты Oracle включают клиент Oracle Database, который используется приложениями SAP для подключения к СУБД Oracle. 

Исключениями, в соответствии с примечанием к SAP [2039619](https://launchpad.support.sap.com/#/notes/2039619), являются компоненты SAP, которые не используют клиент Oracle Database. К таким компонентам SAP относятся: компонент автономной постановки в очередь, сервер сообщений, службы репликации для постановки в очередь, WebDispatcher и шлюз SAP.  

Даже если разместить СУБД Oracle и экземпляры приложений SAP в Oracle Linux, вы можете запустить центральные службы SAP в SLES или RHEL и защитить их с помощью кластера на основе Pacemaker. Pacemaker как платформа высокого уровня доступности не поддерживается в Oracle Linux.

## <a name="specifics-for-oracle-database-on-windows"></a>Особенности Oracle Database в Windows

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Рекомендации по настройке Oracle для установки SAP на виртуальных машинах Azure в Windows

В соответствии с руководством по установке SAP файлы, относящиеся к Oracle, не должны устанавливаться или размещаться в драйвере системы для диска ОС виртуальной машины (диск C:). Виртуальные машины разных размеров могут поддерживать различное количество присоединенных дисков. Виртуальные машины небольшого размера могут поддерживать меньшее количество присоединенных дисков. 

Если у вас виртуальные машины небольшого размера, мы советуем устанавливать (размещать) каталоги Oracle home, stage, saptrace, saparch, sapbackup, sapcheck или sapreorg на диске операционной системы. Эти части компонентов СУБД Oracle не создают интенсивной нагрузки на ввод-вывод и пропускную способность ввода-вывода. Это означает, что диск операционной системы справится с потребностями ввода-вывода. Размер диска операционной системы по умолчанию составляет 127 ГБ. 

Если свободного места недостаточно, размер диска можно [изменить](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk) на 2048 ГБ. Oracle Database и файлы журнала повторяемых операций нужно хранить на отдельных дисках данных. Единственным исключением является временная таблица Oracle. Временные файлы можно создать на диске D:/ (временном диске). Несохраняемый диск D:\ также обеспечивает более низкую задержку операций ввода-вывода и лучшую пропускную способность (за исключением виртуальных машин серии A). 

Чтобы определить правильный объем пространства для временных файлов, можно проверить размеры временных файлов в существующих системах.

### <a name="storage-configuration"></a>Конфигурация хранилища
Поддерживается только один экземпляр Oracle, использующий диски, отформатированные в NTFS. Все файлы базы данных должны храниться в файловой системе NTFS на Управляемых дисках (рекомендуемый вариант) или на виртуальных жестких дисках. Эти виртуальные жесткие диски должны быть подключены к виртуальной машине Azure и созданы на основе [хранилища страничных BLOB-объектов Azure](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) или [Управляемых дисков Azure](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview). 

Мы настоятельно рекомендуем использовать [Управляемые диски Azure](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview), а для развертываний Oracle Database — [диски SSD ценовой категории "Премиум"](../../windows/disks-types.md).

Сетевые диски и удаленные общие ресурсы, включая файловые службы Azure, не поддерживаются для файлов Oracle Database. Дополнительные сведения можно найти в разделе 

- [Введение в службы файлов Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Сохраняемые подключения к файлам Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)


Если вы используете диски на основе хранилища страничных BLOB-объектов Azure или Управляемых дисков, инструкции, изложенные в документе [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](dbms_guide_general.md), также применяются к развертываниям Oracle Database.

Существуют квоты на пропускную способность операций ввода-вывода в секунду для дисков Azure. Эта концепция объясняется в статье [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](dbms_guide_general.md). Квоты зависят от типа используемой виртуальной машины. Список типов виртуальных машин с соответствующими квотами см. в статье [Размеры виртуальных машин Windows в Azure][virtual-machines-sizes-windows].

Чтобы определить поддерживаемые типы виртуальных машин Azure, ознакомьтесь с примечанием к SAP [1928533].

Минимальная конфигурация выглядит следующим образом. 

| Компонент | Диск | Caching | Пул хранилищ |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA & mirrlogB | Премиум | Нет | Не требуется. |
| \oracle\<SID>\origlogaB & mirrlogA | Премиум | Нет | Не требуется. |
| \oracle\<SID>\sapdata1...n | Премиум | Только для чтения | Можно использовать |
| \oracle\<SID>\oraarch | Стандартная | Нет | Не требуется. |
| Oracle Home, saptrace, ... | Диск ОС | | Не требуется. |


Выбор дисков для размещения оперативных журналов повторяемых операций следует основывать на требованиях к операциям ввода-вывода. Есть возможность хранить все табличные пространства (sapdata1...n) на одном подключенном диске, если он соответствует всем требованиям по размеру, операциям ввода-вывода в секунду и пропускной способности. 

Конфигурация производительности выглядит следующим образом.

| Компонент | Диск | Caching | Пул хранилищ |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA | Премиум | Нет | Можно использовать  |
| \oracle\<SID>\origlogaB | Премиум | Нет | Можно использовать |
| \oracle\<SID>\mirrlogAB | Премиум | Нет | Можно использовать |
| \oracle\<SID>\mirrlogBA | Премиум | Нет | Можно использовать |
| \oracle\<SID>\sapdata1...n | Премиум | Только для чтения | Рекомендуется  |
| \oracle\SID\sapdata(n+1)* | Премиум | Нет | Можно использовать |
| \oracle\<SID>\oraarch* | Премиум | Нет | Не требуется. |
| Oracle Home, saptrace, ... | Диск ОС | Не требуется. |

* (n+1): размещение табличных пространств SYSTEM, TEMP и UNDO. Характеристики ввода-вывода для табличных пространств System и Undo существенно отличаются от характеристик других табличных пространств, в которых размещаются данные приложений. Для повышения производительности табличных пространств System и Undo лучше всего отключить кэширование.

* oraarch: пул носителей не является обязательным с точки зрения производительности. Его можно использовать для увеличения доступного пространства.

Если требуется большее число операций ввода-вывода в секунду, мы советуем использовать пулы носителей Windows (доступны только в Windows Server 2012 и более поздних версиях) для создания одного большого логического устройства из нескольких подключенных дисков. Такой подход позволяет сократить административные расходы на управление дисковым пространством и помогает избежать необходимости вручную распределять файлы между несколькими подключенными дисками.


#### <a name="write-accelerator"></a>Ускоритель записи
Для виртуальных машин Azure серии M можно в несколько раз уменьшить задержку при записи в оперативные журналы повторных операций в сравнении со службой хранилища Azure класса Premium. Включите Ускоритель записи Azure для дисков (VHD), которые размещаются в хранилище класса Premium Azure и используются для файлов оперативных журналов повторных операций. Дополнительные сведения см. в статье [Включение Ускорителя записи](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).


### <a name="backuprestore"></a>Резервное копирование и восстановление
Для резервного копирования и восстановления можно использовать средства SAP BR* для Oracle. Они поддерживаются так же, как и в стандартных операционных системах Windows Server. Для резервного копирования на диски и восстановления с дисков также можно использовать диспетчер восстановления Oracle (RMAN).

Для резервного копирования виртуальных машин с согласованием на уровне приложений также можно использовать Azure Backup. В статье [Резервное копирование виртуальных машин Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) объясняется, как Azure Backup использует функциональность Windows VSS для резервного копирования виртуальных машин с согласованием на уровне приложений. Выпуски СУБД Oracle, которые поддерживаются в Azure для SAP, могут использовать функциональность VSS для резервного копирования. Дополнительные сведения см. в разделе об [основных понятиях, связанных с резервным копированием и восстановлением баз данных с помощью VSS](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701) в документации Oracle.


### <a name="high-availability"></a>высокую доступность;
Для обеспечения высокой доступности и аварийного восстановления можно использовать Oracle Data Guard. Чтобы обеспечить автоматический переход на другой ресурс в Data Guard, необходимо использовать FSFA (быстрый запуск отработки отказа). Наблюдатель (FSFA) запускает отработку отказа. Если вы не используете FSFA, вам подходит только конфигурация перехода на другой ресурс вручную.

Дополнительные сведения об аспектах аварийного восстановления баз данных Oracle в Azure см. в статье [Аварийное восстановление базы данных Oracle Database 12c в среде Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery).

### <a name="accelerated-networking"></a>Ускорение работы в сети
Для развертываний Oracle в Windows мы настоятельно рекомендуем использовать ускорение работы в сети, как описано в [этой статье](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Обратите также внимание на рекомендации, приведенные в документе [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](dbms_guide_general.md). 
### <a name="other"></a>Другие
Другие важные концепции, относящиеся к развертываниям виртуальных машин в Oracle Database, включая группы доступности Azure и мониторинг SAP, описываются в статье [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](dbms_guide_general.md).

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Особенности Oracle Database в Oracle Linux
Программное обеспечение Oracle поддерживается Oracle для работы в Microsoft Azure с Oracle Linux в качестве гостевой ОС. Чтобы ознакомиться с дополнительными сведениями об общей поддержке Windows Hyper-V и Azure, изучите [часто задаваемые вопросы по Azure и Oracle](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

Поддерживаются также определенные сценарии приложений SAP, использующих экземпляры Oracle Database. Подробности приведены в следующей части документа.

### <a name="oracle-version-support"></a>Поддерживаемые версии Oracle
Дополнительные сведения о версиях Oracle и соответствующих версиях ОС, которые поддерживаются для работы SAP в Oracle на Виртуальных машинах Azure, см. в примечании к SAP [2039619].

Общие сведения о запуске SAP Business Suite в Oracle можно найти на странице сообщества, посвященной [SAP в Oracle](https://www.sap.com/community/topic/oracle.html).

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Рекомендации по настройке Oracle для установки SAP на виртуальных машинах Azure в Linux

В соответствии с руководствами по установке SAP файлы, относящиеся к Oracle, не должны устанавливаться или размещаться в системных драйверах для загрузочного диска виртуальной машины. Виртуальные машины разных размеров поддерживают различное количество присоединенных дисков. Виртуальные машины небольшого размера могут поддерживать меньшее количество присоединенных дисков. 

Для таких систем мы советуем устанавливать (размещать) каталоги Oracle home, stage, saptrace, saparch, sapbackup, sapcheck или sapreorg на загрузочном диске. Эти части компонентов СУБД Oracle не создают интенсивной нагрузки на ввод-вывод и пропускную способность ввода-вывода. Это означает, что диск операционной системы справится с потребностями ввода-вывода. Размер диска операционной системы по умолчанию составляет 30 ГБ. Загрузочный диск можно увеличить с помощью портала Azure, PowerShell или интерфейса командной строки. После увеличения загрузочного диска вы сможете добавить дополнительный раздел для двоичных файлов Oracle.


### <a name="storage-configuration"></a>Конфигурация хранилища

Файловые системы ext4, xfs и Oracle ASM поддерживаются для файлов Oracle Database в Azure. Все файлы базы данных должны храниться на виртуальных жестких дисках или Управляемых дисках с этими файловыми системами. Эти виртуальные жесткие диски должны быть подключены к виртуальной машине Azure и созданы на основе [хранилища страничных BLOB-объектов Azure](<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) или [Управляемых дисков Azure](../../windows/managed-disks-overview.md).

Ядра UEK Oracle Linux требуют UEK версии не меньше 4 для поддержки [дисков SSD Azure ценовой категории "Премиум"](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-caching).

Настоятельно рекомендуем использовать [Управляемые диски Azure](../../windows/managed-disks-overview.md). Также для развертываний Oracle Database настоятельно рекомендуется использовать [диски SSD Azure ценовой категории "Премиум"](../../windows/disks-types.md).

Сетевые диски и удаленные общие ресурсы, включая файловые службы Azure, не поддерживаются для файлов Oracle Database. Дополнительную информацию см. в следующих разделах. 

- [Введение в службы файлов Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Сохраняемые подключения к файлам Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

Если вы используете диски на основе хранилища страничных BLOB-объектов Azure или Управляемых дисков, инструкции, изложенные в документе [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](dbms_guide_general.md), также применяются к развертываниям Oracle Database.

 Существуют квоты на пропускную способность операций ввода-вывода в секунду для дисков Azure. Эта концепция объясняется в статье [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](dbms_guide_general.md). Точные квоты зависят от типа используемой виртуальной машины. Список типов виртуальных машин с соответствующими квотами см. в статье [Размеры виртуальных машин Linux в Azure][virtual-machines-sizes-linux].

Чтобы определить поддерживаемые типы виртуальных машин Azure, ознакомьтесь с примечанием к SAP [1928533].

Минимальная конфигурация:

| Компонент | Диск | Caching | Чередование* |
| --- | ---| --- | --- |
| /Oracle/\<SID > / origlogaA & mirrlogB | Премиум | Нет | Не требуется. |
| /Oracle/\<SID > / origlogaB & mirrlogA | Премиум | Нет | Не требуется. |
| /oracle/\<SID>/sapdata1...n | Премиум | Только для чтения | Можно использовать |
| /Oracle/\<SID > / oraarch | Стандартная | Нет | Не требуется. |
| Oracle Home, saptrace, ... | Диск ОС | | Не требуется. |

* Чередование: LVM или MDADM на основе RAID0.

Выбор дисков для размещения оперативных журналов повторяемых операций Oracle следует основывать на требованиях к операциям ввода-вывода. Есть возможность хранить все табличные пространства (sapdata1...n) на одном подключенном диске, если он соответствует всем требованиям к объему, операциям ввода-вывода в секунду и пропускной способности. 

Конфигурация производительности:

| Компонент | Диск | Caching | Чередование* |
| --- | ---| --- | --- |
| /Oracle/\<SID > / origlogaA | Премиум | Нет | Можно использовать  |
| /Oracle/\<SID > / origlogaB | Премиум | Нет | Можно использовать |
| /Oracle/\<SID > / mirrlogAB | Премиум | Нет | Можно использовать |
| /Oracle/\<SID > / mirrlogBA | Премиум | Нет | Можно использовать |
| /oracle/\<SID>/sapdata1...n | Премиум | Только для чтения | Рекомендуется  |
| /Oracle/\<SID > / sapdata(n+1) * | Премиум | Нет | Можно использовать |
| /Oracle/\<SID > / oraarch * | Премиум | Нет | Не требуется. |
| Oracle Home, saptrace, ... | Диск ОС | Не требуется. |

* Чередование: LVM или MDADM на основе RAID0.

* (n+1): размещение табличных пространств SYSTEM, TEMP и UNDO. Характеристики ввода-вывода для табличных пространств System и Undo существенно отличаются от характеристик других табличных пространств, в которых размещаются данные приложений. Для повышения производительности табличных пространств System и Undo лучше всего отключить кэширование.

* oraarch: пул носителей не является обязательным с точки зрения производительности.


Если требуется большее количество операций ввода-вывода в секунду, мы советуем использовать диспетчер логических томов или MDADM,чтобы создать один большой логический том на несколько подключенных дисков. Рекомендации и указания по использованию LVM или MDADM см. в статье [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](dbms_guide_general.md). Такой подход позволяет сократить административные расходы на управление дисковым пространством и помогает избежать необходимости вручную распределять файлы между несколькими подключенными дисками.


#### <a name="write-accelerator"></a>Ускоритель записи
Для виртуальных машин Azure серии M можно в несколько раз уменьшить задержку при записи в оперативные журналы повторных операций (по сравнению с производительностью службы хранилища Azure класса Premium), используя Ускоритель записи Azure. Включите Ускоритель записи Azure для дисков (VHD), которые размещаются в хранилище класса Premium Azure и используются для файлов оперативных журналов повторных операций. Дополнительные сведения см. в статье [Включение Ускорителя записи](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).


### <a name="backuprestore"></a>Резервное копирование и восстановление
Для резервного копирования и восстановления можно использовать инструменты SAP BR* для Oracle. Они поддерживаются так же, как и в системе с исходным состоянием и Hyper-V. Для резервного копирования на диски и восстановления с дисков также можно использовать диспетчер восстановления Oracle (RMAN).

Дополнительные сведения об использовании служб архивации и восстановления Azure для резервного копирования и восстановления баз данных Oracle см. в статье [Создание резервных копий и восстановление базы данных Oracle Database 12c на виртуальной машине Linux в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-backup-recovery).

### <a name="high-availability"></a>высокую доступность;
Для обеспечения высокой доступности и аварийного восстановления можно использовать Oracle Data Guard. Чтобы обеспечить автоматический переход на другой ресурс в Data Guard, необходимо использовать FSFA (быстрый запуск отработки отказа). Компонент "Наблюдатель" (FSFA) запускает отработку отказа. Если вы не используете FSFA, вам подходит только конфигурация перехода на другой ресурс вручную. Дополнительные сведения см. в статье [Реализация Oracle Data Guard на виртуальной машине Azure под управлением Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard).


Аспекты аварийного восстановления для баз данных Oracle в Azure описаны в статье [Аварийное восстановление базы данных Oracle Database 12c в среде Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery).

### <a name="accelerated-networking"></a>Ускорение работы в сети
Поддержка ускорения работы в сети Azure в Oracle Linux предоставляется в Oracle Linux 7 с обновлением 5 (Oracle Linux 7.5). Если вы не можете обновить Oracle Linux до версии 7.5, можно попробовать применить RedHat Compatible Kernel (RHCK) вместо ядра Oracle UEK. 

Использование ядра RHEL в Oracle Linux поддерживается в соответствии с примечанием SAP [1565179](https://launchpad.support.sap.com/#/notes/1565179). Для ускорения сети Azure можно использовать ядро RHCKL выпуска не менее 3.10.0-862.13.1.el7. Если вы используете ядро UEK в Oracle Linux вместе с [ускоренной сетью Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/), необходимо ядро Oracle UEK версии 5.

Если вы развертываете виртуальные машины из образа, полученного не из Azure Marketplace, необходимо скопировать на эту виртуальную машину дополнительные файлы конфигурации, выполнив следующий код: 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


### <a name="other"></a>Другие
Другие важные концепции, относящиеся к развертываниям виртуальных машин в Oracle Database, включая группы доступности Azure и мониторинг SAP, описываются в статье [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](dbms_guide_general.md).
