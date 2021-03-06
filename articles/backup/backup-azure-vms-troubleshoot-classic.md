---
title: "클래식 포털에서 Azure 백업 오류 문제 해결 | Microsoft Docs"
description: "클래식 포털에서 Azure 가상 컴퓨터의 Azure 백업 및 복원 문제를 해결합니다."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 117201fb-c0cd-4be4-b47f-abd88fe914cf
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: trinadhk;markgal;
ms.openlocfilehash: 284a1b64fbb15d0aa800182c6671d447e191b76a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Azure 가상 컴퓨터 백업 문제 해결
> [!div class="op_single_selector"]
> * [복구 서비스 자격 증명 모음](backup-azure-vms-troubleshoot.md)
> * [백업 자격 증명 모음](backup-azure-vms-troubleshoot-classic.md)
>
>

아래 표에 나열된 정보를 참조하여 Azure 백업을 사용하는 동안 발생하는 오류를 해결할 수 있습니다.

## <a name="discovery"></a>검색
| 백업 작업 | 오류 세부 정보 | 해결 방법 |
| --- | --- | --- |
| 검색 |새 항목 검색에 실패했습니다. Microsoft Azure 백업에 내부 오류가 발생했습니다. 몇 분간 기다린 다음 작업을 다시 시도하세요. |15분 후 검색 프로세스를 다시 시도합니다. |
| 검색 |새 항목 검색에 실패했습니다. 다른 검색 작업이 이미 진행 중입니다. 현재 검색 작업을 완료할 때까지 잠시 기다려 주세요. |없음 |

## <a name="register"></a>등록
| 백업 작업 | 오류 세부 정보 | 해결 방법 |
| --- | --- | --- |
| 등록 |가상 컴퓨터에 연결된 데이터 디스크 수가 지원되는 제한을 초과했습니다. 이 가상 컴퓨터에서 일부 데이터 디스크를 분리한 후 작업을 다시 시도하세요. Azure 백업은 Azure 가상 컴퓨터에 연결된 데이터 디스크의 백업을 최대 16개까지 지원합니다. |없음 |
| 등록 |Microsoft Azure 백업에서 내부 오류가 발생했습니다. 몇 분간 기다린 후 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요. |이 오류는 프리미엄 LRS에서 지원되지 않는 다음 VM의 구성 중 하나로 인해 발생할 수 있습니다. <br> 복구 서비스 자격 증명 모음을 사용하여 프리미엄 저장소 VM을 백업할 수 있습니다. [자세한 정보](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup) |
| 등록 |에이전트 설치 작업 시간 초과로 인해 등록하지 못했습니다. |가상 컴퓨터의 OS 버전이 지원되는지 확인합니다. |
| 등록 |명령을 실행하지 못했습니다. 이 항목에 대해 다른 작업이 진행 중입니다. 이전 작업이 완료될 때까지 기다리세요. |없음 |
| 등록 |프리미엄 저장소에 저장된 가상 하드 디스크에 있는 가상 컴퓨터는 백업을 지원하지 않습니다. |없음 |
| 등록 |가상 컴퓨터에는 가상 컴퓨터 에이전트가 존재하지 않습니다. 필수 구성 요소, VM 에이전트를 설치하고 작업을 다시 시작하세요. |[자세히 알아보세요](#vm-agent) . |

## <a name="backup"></a>백업
| 백업 작업 | 오류 세부 정보 | 해결 방법 |
| --- | --- | --- |
| 백업 |스냅숏 상태에 대해 VM 에이전트와 통신할 수 없습니다. 스냅숏 VM 하위 작업 시간 초과. 이를 해결하는 방법은 문제 해결 가이드를 참조하세요. |이 오류는 VM 에이전트에 문제가 있거나 Azure 인프라에 대한 네트워크 액세스가 어떤 방식으로든 차단된 경우에 발생합니다. [VM 스냅숏 문제 디버깅 문제](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)에 대해 자세히 알아봅니다. <br> VM 에이전트가 아무 문제도 유발하지 않으면, VM을 다시 시작합니다. 가끔 잘못된 VM 상태가 문제를 일으킬 수 있으며 VM을 다시 시작하여 "잘못된 상태"를 초기화합니다. |
| 백업 |내부 오류가 발생하여 백업하지 못했습니다. 몇 분 후에 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요. |VM 저장소에 액세스하는 데 일시적인 문제가 있는지 확인합니다. [Azure 상태](https://azure.microsoft.com/en-us/status/) 를 확인하여 지역의 계산/저장소/네트워크와 관련하여 진행 중인 문제가 있는지 확인합니다. 문제가 완화되면 백업을 다시 시도합니다. |
| 백업 |VM이 더 이상 존재하지 않아 작업을 수행할 수 없습니다. |백업용으로 구성된 VM이 삭제되었기 때문에 백업을 수행할 수 없습니다. 이 경우 보호된 항목 보기로 이동하여 향후의 백업을 중지시키고, 보호된 항목을 선택하여 보호 중지를 클릭합니다. 백업 데이터 보존 옵션을 선택하여 데이터를 보존할 수 있습니다. 나중에 등록된 항목 보기에서 보호 구성을 클릭하여 이 가상 컴퓨터에 대한 보호를 다시 시작할 수 있습니다. |
| 백업 |선택한 항목에 Azure 복구 서비스 확장을 설치하지 못했습니다. Azure 복구 서비스 확장의 필수 조건인 VM 에이전트가 있어야 합니다. Azure VM 에이전트를 설치하고 등록 작업을 다시 시작하세요. |<ol> <li>VM 에이전트가 제대로 설치되었는지 확인합니다. <li>VM 구성의 플래그가 올바르게 설정되었는지 확인합니다.</ol> [자세히 알아보세요](#validating-vm-agent-installation) . |
| Backup |명령을 실행하지 못했습니다. 현재 이 항목에 대해 다른 작업이 진행 중입니다. 이전 작업이 완료될 때까지 기다린 후 다시 시도하세요. |VM에 대한 기존 백업 또는 복원 작업이 실행 중이며, 기존 작업이 실행되는 동안에는 새 작업을 시작할 수 없습니다. |
| 백업 |“COM+” 오류로 인해 확장 설치가 실패하면 Microsoft Distributed Transaction Coordinator와 통신할 수 없습니다. |이는 대개 COM+ 서비스가 실행되고 있지 않음을 의미합니다. 이 문제 해결에 대한 도움은 Microsoft 지원에 문의하세요. |
| 백업 |“이 드라이브는 BitLocker 드라이브 암호화로 잠겨 있습니다.”라는 VSS 작업 오류와 함께 스냅숏 작업이 실패했습니다. 제어판에서 이 드라이브 잠금을 해제해야 합니다. |VM에 있는 모든 드라이브의 BitLocker를 끄고 VSS 문제가 해결 되었는지 관찰합니다. |
| 백업 |프리미엄 저장소에 저장된 가상 하드 디스크에 있는 가상 컴퓨터는 백업을 지원하지 않습니다. |없음 |
| 백업 |Azure 가상 컴퓨터를 찾을 수 없습니다. |이는 주 VM이 삭제되었지만 백업 정책이 백업을 수행하기 위해 계속 VM을 검색할 때 발생합니다. 이 오류를 해결하려면  <ol><li>동일한 이름 및 동일한 리소스 그룹 이름[클라우드 서비스 이름]으로 가상 컴퓨터를 다시 만듭니다. <br>(또는) <li> 후속 백업이 트리거되지 않도록 이 VM에 대한 보호를 해제합니다. </ol> |
| Backup |가상 컴퓨터에는 가상 컴퓨터 에이전트가 존재하지 않습니다. 필수 구성 요소, VM 에이전트를 설치하고 작업을 다시 시작하세요. |[자세히 알아보세요](#vm-agent) . |

## <a name="jobs"></a>작업
| 작업 | 오류 세부 정보 | 해결 방법 |
| --- | --- | --- |
| 작업 취소 |이 작업 유형에 대해서는 취소가 지원되지 않습니다. 작업이 완료될 때까지 기다려주세요. |없음 |
| 작업 취소 |작업이 취소 가능한 상태에 있지 않습니다. 작업이 완료될 때까지 기다려주세요. <br>또는<br> 선택한 작업이 취소 가능한 상태가 아닙니다. 작업이 완료될 때까지 기다려주세요. |작업이 거의 완료되었습니다. 작업이 완료될 때까지 기다려주세요. |
| 작업 취소 |진행되고 있지 않기 때문에 작업을 취소할 수 없습니다. 취소는 작업이 진행 중인 경우에만 지원됩니다. 진행 중인 작업에 대해 취소를 시도하세요. |이는 일시적인 상태 때문에 발생합니다. 잠시 기다렸다가 취소 작업을 다시 시도하세요. |
| 작업 취소 |작업을 취소하지 못했습니다. 작업이 완료될 때까지 기다려주세요. |없음 |

## <a name="restore"></a>복원
| 작업 | 오류 세부 정보 | 해결 방법 |
| --- | --- | --- |
| 복원 |클라우드 내부 오류로 인해 복원 실패 |<ol><li>복원하려는 클라우드 서비스가 DNS 설정을 사용하여 구성되었습니다. 다음을 확인하면 알 수 있습니다. <br>$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings<br>구성된 주소가 있으면 DNS 설정이 구성되었다는 의미입니다.<br> <li>복원하려는 클라우드 서비스가 ReservedIP를 사용하여 구성되고 클라우드 서비스의 기존 VM이 중단된 상태에 있습니다.<br>다음 powershell cmdlet을 사용하여 클라우드 서비스에 예약된 IP가 있는지 확인할 수 있습니다.<br>$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName <br><li>동일한 클라우드 서비스에 다음과 같이 특수한 네트워크 구성을 사용하여 가상 컴퓨터를 복원하려고 시도하고 있습니다. <br>- 부하 분산 장치 구성의 가상 컴퓨터(내부 및 외부)<br>- 여러 개의 예약된 IP를 사용하는 가상 컴퓨터<br>- 여러 NIC가 있는 가상 컴퓨터<br>특수한 네트워크 구성을 가진 VM의 경우 [복원 고려 사항](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations)을 참조하거나 UI에서 새 클라우드 서비스를 선택하세요</ol> |
| 복원 |선택한 DNS 이름이 이미 사용 되었습니다. 다른 DNS 이름을 지정하고 다시 시도하세요. |여기에서 DNS 이름은 클라우드 서비스 이름을 가리킵니다(일반적으로 cloudapp.net로 끝남). 이름은 고유한 것이어야 합니다. 이러한 오류가 발생하는 경우, 복원하는 동안 다른 VM 이름을 선택해야 합니다. <br><br> 이 오류는 Azure 포털의 사용자에게만 표시됩니다. PowerShell 통한 복원 작업은 디스크만 복원하고 VM을 만들지 않기 때문에 성공합니다. 디스크 복원 작업 후 사용자가 명시적으로 VM를 만들 경우 오류가 발생합니다. |
| 복원 |지정된 가상 네트워크 구성이 올바르지 않습니다. 다른 가상 네트워크 구성에 지정하고 다시 시도하세요. |없음 |
| 복원 |지정된 클라우드 서비스는 복원 중인 가상 컴퓨터의 구성과 일치하지 않는 예약된 IP를 사용하고 있습니다. 예약된 IP를 사용하지 않는 다른 클라우드 서비스를 지정하거나 복원하려면 다른 복구 지점을 선택하세요. |없음 |
| 복원 |클라우드 서비스가 입력된 끝점 제한 수에 도달 했습니다. 다른 클라우드 서비스를 지정하거나 기존 끝점을 사용하여 작업을 다시 시도합니다. |없음 |
| 복원 |서로 다른 두 지역에 백업 저장소와 대상 저장소 계정이 있습니다. 복원 작업에서 지정된 저장소 계정이 백업 저장소와 동일한 Azure 지역에 있는지 확인합니다. |없음 |
| 복원 |복원 작업에 대해 지정된 저장소 계정은 지원되지 않습니다. 로컬 중복 또는 지리적 중복 복제 설정이 있는 기본/표준만 저장소 계정만 지원됩니다. 지원되는 저장소 계정을 선택하세요. |없음 |
| 복원 |복원 작업에 지정된 저장소 계정 유형은 온라인 상태가 아닙니다. 복원 작업에 지정된 저장소 계정이 온라인 상태인지 확인하세요. |이는 Azure 저장소의 일시적인 오류 또는 가동 중지로 인해 발생할 수 있습니다. 다른 저장소 계정을 선택하세요. |
| 복원 |리소스 그룹 할당량에 도달했습니다. Azure 포털의 일부 리소스 그룹을 삭제하거나 Azure 지원에 문의하여 제한을 늘리세요. |없음 |
| 복원 |선택한 서브넷이 존재하지 않습니다. 존재하는 서브넷을 선택하세요. |없음 |

## <a name="policy"></a>정책
| 작업 | 오류 세부 정보 | 해결 방법 |
| --- | --- | --- |
| 정책 만들기 |정책을 만들지 못했습니다. 보존 선택 항목을 줄여 정책을 계속 구성하세요. |없음 |

## <a name="vm-agent"></a>VM 에이전트
### <a name="setting-up-the-vm-agent"></a>VM 에이전트 설정
일반적으로, Azure 갤러리에서 만든 VM에는 VM 에이전트가 이미 있습니다. 그러나 온-프레미스 데이터 센터에서 마이그레이션한 가상 컴퓨터에는 VM 에이전트가 설치되어 있지 않습니다. 이러한 VM의 경우 VM 에이전트를 명시적으로 설치해야 합니다. [기존 VM에 VM 에이전트 설치](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)에 대해 자세히 알아보세요.

Windows VM의 경우

* [에이전트 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하여 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다.
* [VM 속성을 업데이트](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) 하여 에이전트가 설치되었다고 표시합니다.

Linux VM의 경우

* github에서 최신 [Linux 에이전트](https://github.com/Azure/WALinuxAgent) 를 설치합니다.
* [VM 속성을 업데이트](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) 하여 에이전트가 설치되었다고 표시합니다.

### <a name="updating-the-vm-agent"></a>VM 에이전트 업데이트
Windows VM의 경우

* VM 에이전트 업데이트는 [VM 에이전트 이진](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)을 다시 설치하면 되는 간단한 작업입니다. 그러나 VM 에이전트를 업데이트하는 동안 실행 중인 백업 작업이 없도록 해야 합니다.

Linux VM의 경우

* [Linux VM 에이전트 업데이트](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)의 지침을 따르세요.

### <a name="validating-vm-agent-installation"></a>VM 에이전트 설치의 유효성 검사
Windows VM에서 VM 에이전트 버전을 확인하는 방법

1. Azure Virtual Machine에 로그온하고 *C:\WindowsAzure\Packages* 폴더로 이동합니다. WaAppAgent.exe 파일을 찾습니다.
2. 파일을 마우스 오른쪽 단추로 클릭하고 **속성**으로 이동한 다음 **세부 정보** 탭을 선택합니다. 제품 버전 필드가 2.6.1198.718 이상이어야 합니다.
