---
title: "FreeBSD VM 이미지 만들기 및 업로드 | Microsoft Docs"
description: "FreeBSD 운영 체제가 포함된 VHD(가상 하드 디스크)를 만들고 업로드하여 Azure 가상 컴퓨터를 만드는 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: huishao
ms.openlocfilehash: 7b41826f071174df8f00af56a228e0f31c3cfe2f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2017
---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>FreeBSD VHD를 만들어서 Azure에 업로드
이 문서에서는 FreeBSD 운영 체제가 포함된 VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다. VHD를 업로드한 후에는 VHD를 사용자 고유의 이미지로 사용하여 Azure에서 VM(가상 컴퓨터)을 만들 수 있습니다.

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. Resource Manager 모델을 사용하여 VHD을 업로드하는 방법에 대한 정보는 [여기](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 사용자에게 다음 항목이 있다고 가정합니다.

* **Azure 구독**- 계정이 없는 경우 몇 분 만에 계정을 만들 수 있습니다. MSDN 구독이 있는 경우에는 [Visual Studio 구독자를 위한 월간 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요. 그렇지 않으면 [무료 평가판 계정 만들기](https://azure.microsoft.com/pricing/free-trial/)를 참조하세요.  
* **Azure PowerShell 도구**- Azure PowerShell 모듈이 설치되어 있고 Azure 구독을 사용하도록 구성되어 있어야 합니다. 모듈을 다운로드하려면 [Azure 다운로드](https://azure.microsoft.com/downloads/)를 참조하세요. 모듈 설치 및 구성 방법을 설명한 자습서는 여기에서 확인할 수 있습니다. [Azure Downloads](https://azure.microsoft.com/downloads/) cmdlet을 사용하여 VHD를 업로드합니다.
* **.vhd 파일에 설치된 FreeBSD 운영 체제**- 가상 하드 디스크에 지원되는 FreeBSD 운영 체제를 설치해야 합니다. .vhd 파일을 만드는 도구는 여러 가지가 있습니다. 예를 들어 Hyper-V와 같은 가상화 솔루션을 사용하여 .vhd 파일을 만들고 운영 체제를 설치할 수 있습니다. Hyper-V를 설치하고 사용하는 방법에 대한 자세한 내용은 [Hyper-V 설치 및 가상 컴퓨터 만들기](http://technet.microsoft.com/library/hh846766.aspx)를 참조하세요.

> [!NOTE]
> 새 VHDX 형식은 Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx)cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다. 뿐만 아니라 [Hyper-V에 FreeBSD를 사용하는 방법에 대한 MSDN 자습서](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx)가 있습니다.
>
>

이 작업에는 다음 4단계가 포함됩니다.

## <a name="step-1-prepare-the-image-for-upload"></a>1단계: 업로드할 이미지 준비
FreeBSD 운영 체제를 설치한 가상 컴퓨터에서 다음 절차를 완료합니다.

1. DHCP를 활성화합니다.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. SSH를 활성화합니다.

    SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. FreeBSD 디스크에서 설치 후에는 기본적으로 사용하도록 설정되어 있습니다. 
3. 직렬 콘솔을 설치합니다.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. sudo를 설치합니다.

    Azure에서 root 계정이 비활성화됩니다. 다시 말해서 상승된 권한으로 명령을 실행하려면 권한 없는 사용자로 sudo를 사용해야 합니다.

        # pkg install sudo
   
5. Azure 에이전트에 대한 필수 조건.

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Azure 에이전트를 설치합니다.

    언제든지 최신 버전의 Azure 에이전트를 [github](https://github.com/Azure/WALinuxAgent/releases)에서 확인할 수 있습니다. 버전 2.0.10 이상은 FreeBSD 10 및 10.1을 공식적으로 지원하며, 버전 2.1.4 이상(2.2.x 포함)은 FreeBSD 10.2 이상 릴리스를 공식적으로 지원합니다.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    2.0의 경우 2.0.16을 예로 사용하겠습니다.

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    2.1의 경우 2.1.4를 예로 사용하겠습니다.

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > Azure 에이전트를 설치한 후 에이전트가 실행 중인지 확인하는 것이 좋습니다.
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. 시스템을 프로비전 해제합니다.

    시스템을 프로비전 해제하여 다시 프로비전하기에 적합한 상태로 정리합니다. 다음 명령은 마지막으로 프로비전된 사용자 계정 및 관련 데이터를 삭제합니다.

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    이제 VM을 종료할 수 있습니다.

## <a name="step-2-prepare-the-connection-to-azure"></a>2단계: Azure 연결 준비
클래식 배포 모델(`azure config mode asm`)에서 Azure CLI를 사용하고 있는지 확인한 다음 계정에 로그인합니다.

```azurecli
azure login
```


<a id="upload"> </a>


## <a name="step-3-upload-the-vhd-file"></a>3단계: .vhd 파일 업로드

VHD 파일을 업로드할 저장소 계정이 필요합니다. 기존 저장소 계정을 선택하거나 [새 계정을 만들 수 있습니다](../../../storage/common/storage-create-storage-account.md).

.vhd 파일을 업로드할 때 Blob 저장소 내 임의의 위치에 .vhd 파일을 배치할 수 있습니다. 다음은 파일을 업로드할 때 사용되는 몇 가지 용어입니다.

* **BlobStorageURL**은 2단계에서 만든 저장소 계정의 URL입니다.
* **YourImagesFolder**는 이미지를 저장하려는 Blob 저장소 내 컨테이너입니다.
* **VHDName**은 가상 하드 디스크를 식별하기 위해 Azure 클래식 포털에 표시되는 레이블입니다.
* **PathToVHDFile**은 .vhd 파일의 전체 경로 및 이름입니다.

이전 단계에서 사용한 Azure PowerShell 창에서 다음을 입력합니다.

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-4-create-a-vm-with-the-uploaded-vhd-file"></a>4단계: 업로드한 .vhd 파일로 VM 만들기
.vhd 파일을 업로드한 후에는 구독과 연결된 사용자 지정 이미지 목록에 .vhd 파일을 이미지로 추가하고 이 사용자 지정 이미지를 사용하여 가상 컴퓨터를 만들 수 있습니다.

1. 이전 단계에서 사용한 Azure PowerShell 창에서 다음을 입력합니다.

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > OS 유형으로 Linux를 사용합니다. Azure PowerShell 최신 버전은 매개 변수로 "Linux" 또는 "Windows"만 허용합니다.
   >
   >
2. 이전 단계를 완료한 후 Azure 클래식 포털에서 **이미지** 탭을 선택하면 새 이미지가 나열됩니다.  

    ![이미지 선택](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. 갤러리에서 가상 컴퓨터를 만듭니다. 이제 **내 이미지**에서 이 새 이미지를 사용할 수 있습니다.
4. 새 이미지를 선택합니다. 다음으로 메시지에 따라 호스트 이름, 암호, SSH 키 등을 설정합니다.

    ![사용자 지정 이미지](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. 프로비전이 완료되면 FreeBSD VM이 Azure에서 실행됩니다.

    ![Azure의 FreeBSD 이미지](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)
