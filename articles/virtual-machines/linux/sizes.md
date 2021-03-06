---
title: "Azure의 Linux VM 크기 | Microsoft Docs"
description: "Azure의 Linux 가상 컴퓨터에 사용할 수 있는 다양한 크기를 나열합니다."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: jonbeck
ms.openlocfilehash: 7c6f2e83e5763f3cf8f305c73c0ad74fa8b37c00
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2017
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Azure에서 Linux 가상 컴퓨터에 대한 크기
이 문서에서는 Linux 앱 및 워크로드를 실행하는 데 사용할 수 있는 Azure 가상 컴퓨터에 대한 크기 및 옵션을 설명합니다. 또한 이러한 리소스의 사용 계획을 세울 때 알아야 할 배포 고려 사항도 제공합니다.또한 이러한 리소스의 사용 계획을 세울 때 알아야 할 배포 고려 사항도 제공합니다. 이 문서는 [Windows 가상 컴퓨터](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에도 적용됩니다.


| 형식                     | 크기           |    설명       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [범용](sizes-general.md)          | B(미리 보기), Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7,  | CPU 대 메모리 비율이 적당합니다. 테스트 및 개발, 중소 규모 데이터베이스 및 트래픽이 적거나 중간 정도인 웹 서버에 적합합니다. |
| [Compute에 최적화](sizes-compute.md)        | Fsv2, Fs, F             | CPU 대 메모리 비율이 높습니다. 트래픽이 중간 정도인 웹 서버, 네트워크 어플라이언스, 일괄 처리 프로세스 및 응용 프로그램 서버에 적합합니다.        |
| [메모리에 최적화](sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | 메모리 대 CPU 비율이 높습니다. 관계형 데이터베이스 서버, 중대형 캐시 및 메모리 내 분석에 적합합니다.                 |
| [Storage에 최적화](sizes-storage.md)        | Ls                | 높은 디스크 처리량 및 IO 빅 데이터, SQL, NoSQL 데이터베이스에 적합합니다.                                                         |
| [GPU](sizes-gpu.md)            | NV, NC            | 대량의 그래픽 렌더링 및 비디오 편집에 적합한 전문 가상 컴퓨터로, 한 개 이상의 GPU를 사용할 수 있습니다.       |
| [고성능 계산](sizes-hpc.md) | H, A8-11          | Microsoft의 가장 빠르고 강력한 CPU 가상 컴퓨터로, 필요한 경우 처리량이 높은 네트워크 인터페이스(RDMA)도 제공합니다. 

<br>

- 다양한 크기의 가격 책정에 대한 자세한 내용은 [Virtual Machines 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)을 참조하세요. 
- Azure 지역의 VM 크기 가용성에 대해서는 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.
- Azure VM에 대한 일반적인 제한은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-subscription-service-limits.md)을 참조하세요.
- [ACU(Azure Compute 단위)](../windows/acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.


## <a name="rest-api"></a>Rest API

REST API를 사용하여 VM 크기에 대해 쿼리하는 방법은 다음을 참조하세요.

- [크기 조정에 사용 가능한 가상 컴퓨터 크기 나열](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-for-resizing)
- [구독에 사용 가능한 가상 컴퓨터 크기 나열](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)
- [가용성 집합에서 사용 가능한 가상 컴퓨터 크기 나열](
https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-list-sizes-availability-set)

## <a name="acu"></a>ACU

[ACU(Azure Compute 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.

## <a name="next-steps"></a>다음 단계

사용할 수 있는 다른 VM 크기에 대한 자세한 정보:
- [범용](sizes-general.md)
- [Compute에 최적화](sizes-compute.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [고성능 계산](sizes-hpc.md)



