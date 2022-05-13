---
lab:
  title: 랩 환경 설정
  module: Setup
ms.openlocfilehash: 57363ce9fec94cb3a1a6ef7622a10bca48a6f055
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "137819577"
---
# <a name="lab-environment-setup"></a>랩 환경 설정

이 과정의 연습은 호스트된 랩 환경에서 완료해야 합니다. 개인 컴퓨터에서 연습을 완료하려는 경우 다음 소프트웨어를 설치하면 됩니다. 사용자의 개인 환경을 사용할 경우 예기치 않은 대화 상자와 동작이 발생할 수 있습니다. 다양한 로컬 구성이 가능하기 때문에 과정 팀은 사용자 환경에서 발생할 수 있는 문제를 지원할 수 없습니다.

> **참고**: 아래 지침은 Windows 10용입니다. Linux 또는 MacOS를 사용할 수도 있습니다. 선택한 OS에 맞게 랩 지침을 조정해야 할 수도 있습니다.

### <a name="base-operating-system-windows-10"></a>기본 운영 체제(Windows 10)

#### <a name="windows-10"></a>Windows 10

Windows 10을 설치하고 모든 업데이트를 적용합니다.

#### <a name="edge"></a>Edge

[Edge(Chromium)](https://microsoft.com/edge)을 설치합니다.

### <a name="net-core-sdk"></a>.NET Core SDK

1. https://dotnet.microsoft.com/download 에서 다운로드 및 설치(런타임뿐만 아니라 .NET Core SDK 다운로드)

### <a name="c-redistributable"></a>C++ 재배포 가능 패키지

1. https://aka.ms/vs/16/release/vc_redist.x64.exe 에서 Visual C++ 재배포 가능 패키지(x64) 다운로드 및 설치

### <a name="nodejs"></a>Node.JS

1. https://nodejs.org/en/download/ 에서 최신 LTS 버전 다운로드 
2. 기본 옵션을 사용하여 설치합니다.

### <a name="python-and-required-packages"></a>Python(및 필수 패키지)

1. https://docs.conda.io/en/latest/miniconda.html 에서 버전 3.8 다운로드 
2. 설치 프로그램을 실행하여 설치합니다. **중요**: PATH 변수에 Miniconda를 추가하고 Miniconda를 기본 Python 환경으로 등록하는 옵션을 선택합니다.
3. 설치 후 Anaconda 프롬프트를 열고 다음 명령을 입력하여 패키지를 설치합니다. 

```
pip install flask requests python-dotenv pylint matplotlib pillow
pip install --upgrade numpy
```

### <a name="azure-cli"></a>Azure CLI

1. https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest 에서 다운로드 
2. 기본 옵션을 사용하여 설치합니다.

### <a name="git"></a>Git

1. https://git-scm.com/download.html 에서 기본 옵션을 사용하여 다운로드 및 설치


### <a name="visual-studio-code-and-extensions"></a>Visual Studio Code(및 확장)

1. https://code.visualstudio.com/Download 에서 다운로드 
2. 기본 옵션을 사용하여 설치합니다. 
3. 설치 후 Visual Studio Code를 시작하고 **확장** 탭(Ctrl+Shift+X를 누르면 표시됨)에서 다음 Microsoft 확장을 검색하여 설치합니다.
    - Python
    - C#
    - Azure 기능
    - PowerShell


### <a name="bot-framework-emulator"></a>Bot Framework Emulator

https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md 의 지침에 따라 운영 체제에 대한 안정적인 최신 버전의 Bot Framework Emulator 다운로드하고 설치합니다.

### <a name="bot-framework-composer"></a>Bot Framework 작성기

https://docs.microsoft.com/en-us/composer/install-composer에서 설치합니다.
