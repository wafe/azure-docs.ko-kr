---
title: "Azure IoT Edge + Linux 빠른 시작 | Microsoft Docs"
description: "시뮬레이트된 에지 장치에서 분석을 실행하여 Azure IoT Edge를 시도합니다."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb93efcf00cb7b165c497d7ef38685f80bce84c0
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-linux-device---preview"></a>빠른 시작: Azure Portal에서 Linux 장치(미리 보기)로 첫 번째 IoT Edge 모듈을 배포합니다.

Azure IoT Edge는 클라우드의 강력한 기능을 사물 인터넷 장치로 옮겨놓습니다. 이 항목에서는 클라우드 인터페이스를 사용하여 미리 작성된 코드를 IoT Edge 장치에 원격으로 배포하는 방법을 알아봅니다.

활성 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][lnk-account]을 만드세요.

## <a name="prerequisites"></a>필수 조건

이 작업을 수행하려면 컴퓨터 또는 가상 컴퓨터를 사용하여 사물 인터넷을 시뮬레이션하세요. IoT Edge 장치를 정상적으로 배포하려면 다음 서비스가 필요합니다.

- [Linux에 Docker][lnk-docker-ubuntu]를 설치하고, 지금 실행 중인지 확인합니다. 
- Ubuntu를 포함한 대부분의 Linux 배포에는 Python 2.7이 이미 설치되어 있습니다. 다음 명령을 사용하여 pip가 설치되었는지 확인합니다. `sudo apt-get install python-pip`

## <a name="create-an-iot-hub-with-azure-cli"></a>Azure CLI를 사용하여 IoT Hub 만들기

Azure 구독에서 IoT Hub를 만듭니다. 이 빠른 시작에는 무료 수준의 IoT Hub가 작동합니다. 전에 IoT Hub를 사용했으며 이미 무료 허브를 만든 경우, 이 섹션을 건너뛰고 [IoT Edge 장치 등록][anchor-register]으로 이동할 수 있습니다. 구독마다 하나의 무료 IoT Hub만 가질 수 있습니다. 

1. [Azure Portal][lnk-portal]에 로그인합니다. 
1. **Cloud Shell** 단추를 선택합니다. 

   ![Cloud Shell 단추][1]

1. 리소스 그룹을 만듭니다. 다음 코드는 **미국 서부** 지역에 **IoTEdge**라는 리소스 그룹을 만듭니다.

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. 새 리소스 그룹에 IoT Hub를 만듭니다. 다음 코드는 리소스 그룹 **IoTEdge**에 **MyIotHub**라는 무료 **F1** 허브를 만듭니다.

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>IoT Edge 장치 등록

IoT Hub와 통신할 수 있도록, 시뮬레이트된 장치의 장치 ID를 만듭니다. IoT Edge 장치는 일반적인 IoT 장치와 다르게 작동하며 다른 방식으로 관리될 수 있으므로, 처음부터 IoT Edge 장치로 선언합니다. 

1. Azure Portal에서 IoT Hub로 이동합니다.
1. **IoT Edge(미리 보기)**를 선택합니다.
1. **IoT Edge 장치 추가**를 선택합니다.
1. 시뮬레이트된 장치에 고유한 장치 ID를 제공합니다.
1. **저장**을 선택하여 장치를 추가합니다.
1. 장치 목록에서 새 장치를 선택합니다. 
1. **Connection string--primary key**의 값을 복사하여 저장합니다. 다음 섹션에서 이 값을 사용하여 IoT Edge 런타임을 구성할 것입니다. 

## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge 런타임을 설치하고 시작합니다.

IoT Edge 런타임은 모든 IoT Edge 장치에 배포되며, 두 개의 모듈로 구성됩니다. 첫째, IoT Edge 에이전트는 IoT Edge 장치에서 모듈의 배포 및 모니터링을 지원합니다. 둘째, IoT Edge 허브는 IoT Edge 장치의 모듈 간 통신과 장치와 IoT Hub 간의 통신을 관리합니다. 

IoT Edge 장치를 실행할 컴퓨터에서 IoT Edge 컨트롤 스크립트를 다운로드합니다.
```python
sudo pip install -U azure-iot-edge-runtime-ctl
```

이전 섹션의 IoT Edge 장치 연결 문자열로 런타임을 구성합니다.
```python
sudo iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

런타임을 시작합니다.
```python
sudo iotedgectl start
```

Docker를 확인하여 IoT Edge 에이전트가 모듈로 실행되고 있는지 알아봅니다.
```python
sudo docker ps
```

## <a name="deploy-a-module"></a>모듈 배포

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>생성된 데이터 보기

이 빠른 시작에서는 새 IoT Edge 장치를 만들고 여기에 IoT Edge 런타임을 설치했습니다. 그런 다음 장치 자체를 변경하지 않고도 장치에서 실행할 IoT Edge 모듈을 푸시할 수 있도록 Azure Portal을 사용했습니다. 이 경우 푸시한 모듈에서는 자습서에 대해 사용할 수 있는 환경 데이터를 만듭니다. 

tempSensor 모듈에서 전송되는 메시지를 봅니다.

```cmd/sh
sudo docker logs -f tempSensor
```

[IoT Hub 탐색기 도구][lnk-iothub-explorer]를 사용하여 장치에서 보내는 원격 분석을 볼 수도 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

생성한 IoT Hub가 더 이상 필요하지 않은 경우 [az iot hub delete][lnk-delete] 명령을 사용하여 리소스 및 리소스와 연결된 모든 장치를 제거할 수 있습니다.

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>다음 단계

지금까지 IoT Edge 모듈을 IoT Edge 장치에 배포하는 방법을 배웠습니다. 이제 에지에서 데이터를 분석할 수 있도록 서로 다른 유형의 Azure 서비스를 모듈로 배포해보세요. 

* [자신의 고유한 코드를 모듈로 배포](tutorial-csharp-module.md)
* [Azure Functions를 모듈로 배포](tutorial-deploy-function.md)
* [Azure Stream Analytics를 모듈로 배포](tutorial-deploy-stream-analytics.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
