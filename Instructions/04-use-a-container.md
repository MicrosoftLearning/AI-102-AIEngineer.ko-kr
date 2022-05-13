---
lab:
  title: Cognitive Services 컨테이너 사용
  module: Module 2 - Developing AI Apps with Cognitive Services
ms.openlocfilehash: 3c9e51562e4fb93809855e3ae8707d6aeaf83c97
ms.sourcegitcommit: e9fc70625e09e4f4585a08e1668597f5d82616a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/11/2022
ms.locfileid: "141605466"
---
# <a name="use-a-cognitive-services-container"></a>Cognitive Services 컨테이너 사용

Azure에서 호스트되는 Cognitive Services를 사용하는 애플리케이션 개발자는 직접 작성하는 코드용 인프라 관련 작업을 중점적으로 수행하는 동시에 Microsoft에서 관리하는 확장성 있는 서비스도 활용할 수 있습니다. 그러나 조직에서는 서비스 인프라 및 서비스 간에 전달되는 데이터를 더욱 철저하게 제어해야 하는 경우가 많습니다.

대다수 Cognitive Services API는 *컨테이너* 로 패키지하여 배포할 수 있습니다. 따라서 조직은 로컬 Docker 서버, Azure Container Instances, Azure Kubernetes Services 클러스터 등의 자체 인프라에서 Cognitive Services를 호스트할 수 있습니다. 컨테이너화된 Cognitive Services는 청구 지원을 위해 Azure 기반 Cognitive Services 계정과 통신해야 합니다. 그러나 애플리케이션 데이터는 백 엔드 서비스로 전달되지 않으며 조직은 컨테이너 배포 구성을 더욱 자세히 제어할 수 있습니다. 그러므로 인증, 확장성 및 기타 고려 사항 충족을 위한 사용자 지정 솔루션을 활용할 수 있습니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="provision-a-cognitive-services-resource"></a>Cognitive Services 리소스 프로비전

구독에 아직 없는 경우 **Cognitive Services** 리소스를 프로비전해야 합니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고 *cognitive services* 를 검색한 후에 다음 설정을 사용하여 **Cognitive Services** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 사용 가능한 지역을 선택합니다.
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S0
3. 필요한 확인란을 선택하고 리소스를 만듭니다.
4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.
5. 리소스가 배포되면 해당 리소스로 이동하여 **키 및 엔드포인트** 페이지를 확인합니다. 다음 절차에서 이 페이지에 표시되는 키 중 하나와 엔드포인트가 필요합니다.

## <a name="deploy-and-run-a-text-analytics-container"></a>Text Analytics 컨테이너 배포 및 실행

흔히 사용되는 대다수 Cognitive Services API는 컨테이너 이미지에서 제공됩니다. 전체 API 목록은 [Cognitive Services 설명서](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#container-availability-in-azure-cognitive-services)에서 확인할 수 있습니다. 이 연습에서는 Text Analytics *언어 감지* API용 컨테이너 이미지를 사용하지만 사용 가능한 모든 이미지에는 동일한 원칙이 적용됩니다.

1. Azure Portal **홈** 페이지에서 **&#65291;리소스 만들기** 단추를 선택하고 *container instances* 를 검색한 후에 다음 설정을 사용하여 **Container Instances** 리소스를 만듭니다.

    - **기본 사항**:
        - **구독**: ‘Azure 구독’
        - **리소스 그룹**: Cognitive Services 리소스가 포함된 리소스 그룹 선택
        - **컨테이너 이름**: 고유한 이름 입력
        - **지역**: 사용 가능한 지역을 선택합니다.
        - **이미지 소스**: Docker Hub 또는 다른 레지스트리
        - **이미지**: `mcr.microsoft.com/azure-cognitive-services/textanalytics/language:1.1.013570001-amd64`
        - **OS 유형**: Linux
        - **Size**: vCPU 1개, 메모리 4GB
    - **네트워킹**:
        - **네트워킹 유형**: 공개
        - **DNS 이름 레이블**: 컨테이너 엔드포인트의 고유한 이름 입력
        - **포트**: TCP 포트를 80에서 **5000** 으로 변경
    - **고급**:
        - **정책 다시 시작**: 실패 시
        - **환경 변수**:

            | 원본으로 표시 | 키 | 값 |
            | -------------- | --- | ----- |
            | 예 | `ApiKey` | Cognitive Services 리소스용 키 중 하나 |
            | 예 | `Billing` | Cognitive Services 리소스의 엔드포인트 URI |
            | 예 | `Eula` | `accept` |

        - **명령 재정의**: [ ]
    - **태그**:
        - 태그 추가 안 함

2. 배포가 완료될 때까지 기다린 다음, 배포된 리소스로 이동합니다.
3. 컨테이너 인스턴스 리소스의 **개요** 페이지에서 다음 속성을 살펴봅니다.
    - **상태**: *실행 중* 이어야 합니다.
    - **IP 주소**: Container Instances에 액세스하는 데 사용할 수 있는 공용 IP 주소입니다.
    - **FQDN**: Container Instances 리소스의 *정규화된 도메인 이름* 입니다. IP 주소 대신 이 이름을 사용하여 Container Instances에 액세스할 수 있습니다.

    > **참고**: 이 연습에서는 ACI(Azure Container Instances) 리소스에 텍스트 번역용 Cognitive Services 컨테이너 이미지를 배포했습니다. 비슷한 접근 방식을 사용하여 개인 컴퓨터나 네트워크의 *[Docker](https://www.docker.com/products/docker-desktop)* 호스트에 이미지를 배포할 수도 있습니다. 이렇게 하려면 다음 명령(한 줄로)을 실행하여 로컬 Docker 인스턴스에 언어 감지 컨테이너를 배포합니다. 이때 *&lt;yourEndpoint&gt;* 및 *&lt;yourKey&gt;* 는 각각 Cognitive Services 리소스의 엔드포인트 URI와 키 중 하나로 바꿉니다.
    >
    > ```
    > docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/textanalytics/language Eula=accept Billing=<yourEndpoint> ApiKey=<yourKey>
    > ```
    >
    > 이 명령은 로컬 컴퓨터에서 이미지를 찾은 후 이미지가 없으면 *mcr.microsoft.com* 이미지 레지스트리에서 이미지를 끌어와 Docker 인스턴스에 배포합니다. 배포가 완료되면 컨테이너가 시작되어 포트 5000에서 들어오는 요청을 수신 대기합니다.

## <a name="use-the-container"></a>컨테이너 사용

1. Visual Studio Code의 **04-containers** 폴더에서 **rest-test.cmd** 를 열고 이 파일에 포함되어 있는 **curl** 명령(아래에 나와 있음)을 편집합니다. 이때 *&lt;your_ACI_IP_address_or_FQDN&gt;* 을 컨테이너의 IP 주소나 FQDN으로 바꿉니다.

    ```
    curl -X POST "http://<your_ACI_IP_address_or_FQDN>:5000/text/analytics/v3.0/languages?" -H "Content-Type: application/json" --data-ascii "{'documents':[{'id':1,'text':'Hello world.'},{'id':2,'text':'Salut tout le monde.'}]}"
    ```

2. 스크립트 변경 내용을 저장합니다. 요청은 컨테이너화된 서비스에서 처리되므로 Cognitive Services 엔드포인트나 키는 지정하지 않아도 됩니다. 컨테이너는 Azure의 서비스와 주기적으로 통신하여 청구용으로 사용량을 보고하지만 요청 데이터를 전송하지는 않습니다.
3. **04-containers** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다. 그런 후 다음 명령을 입력하여 스크립트를 실행합니다.

    ```
    rest-test
    ```

4. 명령이 입력 문서 2개에서 감지된 언어(영어 및 프랑스어) 관련 정보가 포함된 JSON 문서를 반환함을 확인합니다.

## <a name="clean-up"></a>정리

컨테이너 인스턴스를 사용한 실험을 완료한 후에는 컨테이너 인스턴스를 삭제해야 합니다.

1. Azure Portal에서 이 연습용으로 리소스를 만든 리소스 그룹을 엽니다.
2. 컨테이너 인스턴스 리소스를 선택하여 삭제합니다.

## <a name="more-information"></a>자세한 정보

Cognitive Services를 컨테이너화하는 방법에 대한 자세한 내용은 [Cognitive Services 컨테이너 설명서](https://docs.microsoft.com/azure/cognitive-services/containers/)를 참조하세요.
