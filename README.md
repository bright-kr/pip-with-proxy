# pip에서 프록시 사용하기

[![Promo](https://github.com/bright-kr/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.co.kr/) 

이 가이드는 pip에서 プロキ시를 설정하여 제한을 우회하고, 보안을 강화하며, 패키지 관리를 간소화하는 방법을 설명합니다:

- [Public vs Private Proxies](#public-vs-private-proxies)
- [Using Proxies with pip](#using-proxies-with-pip)
- [Configuring a pip Proxy with the Command Line](#configuring-a-pip-proxy-with-the-command-line)
- [Configuring a pip Proxy with the pip Config File](#configuring-a-pip-proxy-with-the-pip-config-file)
- [Configuring a pip Proxy with Environment Variables](#configuring-a-pip-proxy-with-environment-variables)
- [Testing the Configuration](#testing-the-configuration)
- [Troubleshooting pip Proxies](#troubleshooting-pip-proxies)
- [Using pip with Rotating Proxies](#using-pip-with-rotating-proxies)
- [Benefits of Using Proxies with pip](#benefits-of-using-proxies-with-pip)
- [Common Mistakes and Best Practices](#common-mistakes-and-best-practices)
- [Using Bright Data Proxies](#using-bright-data-proxies)
- [Conclusion](#conclusion)

## Public vs Private Proxies

### Public Proxies

Public プロキ시는 누구나 접근할 수 있으며 일반적으로 인증이 필요하지 않습니다. 대체 IP 주소를 사용하는 빠른 방법을 제공하지만, 속도가 느리고 연결이 불안정하며 IP 차단 위험이 더 높은 등 눈에 띄는 단점이 있습니다. 무료로 널리 제공되는 경우가 많아 プロキ시 로테이션, 캐싱, 접근 제어와 같은 핵심 기능이 부족한 경우가 많으며, 그로 인해 프로덕션 환경에서 신뢰성 있게 사용하기에는 부적합합니다.

Public URL은 다음과 같은 형식일 수 있습니다: `https://proxyserver:port`.

### Private Proxies

[Private プロキ시](https://brightdata.co.kr/solutions/private-proxies)는 인증이 필요하며, 더 강화된 보안, 안정성, 고급 기능을 제공하지만 보통 비용이 발생합니다. 빠르고 신뢰할 수 있는 연결로 [전용 IP 주소](https://brightdata.co.kr/solutions/dedicated-proxies)에 접근할 수 있으며, 성능과 제어를 개선하기 위한 プロキ시 인증 및 로테이션 같은 기능을 포함합니다.

접근은 일반적으로 인증으로 제어되며, 종종 사용자 이름과 비밀번호를 プロキ시 URL의 접두사로 포함하는 방식으로 구성됩니다. 예: `https://username:password@proxyserver:port`.

## Using Proxies with pip

pip에서 プロ키시를 사용하기 전에, プロ키시에 대한 관련 정보를 수집해야 합니다. 다음 예시는 아래 설정으로 Public プロ키시를 사용하는 방법을 보여줍니다:

- プロ키시 서비스의 **プロ키시 주소**
- 통신에 필요한 プロ키시 서비스의 **port**

다음 [`proxy-list` repo](https://github.com/clarketm/proxy-list)는 매일 테스트된 Public プロ키시 주소를 제공하며 테스트에는 유용할 수 있지만, 프로덕션 환경에서는 사용하지 않아야 합니다.

`proxy-list` repo 내에서, 동작하는 Public プロ키시를 찾기 위해 [`proxy-list-status.txt`](https://github.com/clarketm/proxy-list/blob/master/proxy-list-status.txt) 파일을 확인하십시오. 옆에 `success` 플래그가 표시된 주소를 찾으면 되며, 이는 해당 주소가 동작 중임을 의미합니다:

![Selecting a public proxy](https://github.com/bright-kr/pip-with-proxy/blob/main/Images/Selecting-a-public-proxy.png)

이 튜토리얼에서는 Public プロ키시 주소로 `45.185.162.203:999` 를 사용합니다. 이는 プロ키시 서버 주소가 `http://45.185.162.203:999` 임을 의미합니다.

## Configuring a pip Proxy with the Command Line

pip プロ키시를 가장 빠르게 설정하는 방법은 `pip install` 명령을 호출할 때 `--proxy` 커맨드 라인 옵션으로 주소를 전달하는 것입니다.

Public プロ키시를 통한 접근을 확인하고 패키지 가져오기를 테스트하려면 다음 명령을 실행하십시오:

```
# Public Proxy
pip install boto3 --proxy http://45.185.162.203:999
```

이 접근 방식은 プロ키시를 영구적으로 설정하기 전에 빠르게 테스트하고 검증하는 데 유용합니다. pip 패키지를 배포하는 경우에도, 다른 IP 주소에서의 가용성을 확인하는 데 도움이 됩니다.

## Configuring a pip Proxy with the pip Config File

pip プロ키시를 영구적으로 구성하려면, pip config file이 간단하고 선언적인 해결책입니다. 위치는 운영체제에 따라 다르며 다음 디렉터리에서 찾을 수 있습니다:

- **Global:** 시스템 전역 설정 파일로, 사용자 간에 공유됩니다.
- **User:** pip 프로세스를 실행하는 사용자별 설정 파일입니다.
- **Site:** Python 가상 환경을 사용하는 환경별 설정 파일입니다.

각 시스템에서 이러한 설정 파일은 다음 위치에서 찾거나 생성할 수 있습니다:

### Linux/macOS

Linux 및 macOS에서 pip config file은 `pip.conf`라고 하며 다음 위치에서 찾을 수 있습니다:

- **Global:**
    - **Debian-based systems:** `/etc\` 디렉터리에서 `pip.conf in the`를 편집하거나 생성하십시오.
    - **macOS-based systems:** `/Library/Application Support/pip/pip.conf`를 편집하거나 생성하십시오.
- **User:**
    - **Debian-based systems:** `~/pip/pip.conf` 파일을 편집하거나 생성하십시오.
    - **macOS-based systems:** `~/.config/pip/pip.conf` config file을 편집하거나 생성하십시오.
- **Site:** Python 가상 환경에서 로드될 때 `$VIRTUAL_ENV/pip.conf`에 위치합니다.

### Windows

Windows 시스템에서 파일은 `pip.ini`이며 다음 위치에서 찾을 수 있습니다:

- **Global:** `C:\ProgramData\pip\pip.ini` 파일을 편집하거나 생성하십시오. 이 파일은 Windows 시스템에서 기본적으로 숨김 처리되어 있지만 쓰기 가능합니다.
- **User:** `%APPDATA%\pip\`에 `pip.ini`를 편집하거나 생성하십시오.
- **Site:** Python 가상 환경에서 로드될 때 `%VIRTUAL_ENV%\pip.ini`에서 config file을 편집하거나 생성하십시오.

### Config File Contents

이 예시는 Python 가상 환경의 pip config file을 사용한다고 가정합니다. 활성화된 가상 환경에서 Debian-based systems는 `$VIRTUAL_ENV/pip.conf`를, Windows는 `%VIRTUAL_ENV%\pip.ini`를 편집하십시오.

config file에서 원하는 HTTP 또는 HTTPS プロ키시로 `proxy` 키를 업데이트해야 합니다:

```
[global]
proxy = http://45.185.162.203:999
```

파일을 저장한 후에는 모든 pip 명령에 자동으로 プロ키시가 적용되므로, 매번 プロ키시 플래그를 지정할 필요가 없어집니다:

```bash
(venv) $ pip install boto3
```

구성 옵션에 대한 자세한 내용은 [project’s documentation](https://pip.pypa.io/en/stable/topics/configuration/)을 참조하십시오.

## Configuring a pip Proxy with Environment Variables

시스템 environment variables를 구성하면 pip 및 시스템의 다른 모든 HTTP 요청에 대해 プロ키시가 사용되도록 보장합니다. 이는 `HTTP_PROXY` 및 `HTTPS_PROXY` 변수를 설정하여 달성되며, pip를 포함한 많은 애플리케이션이 HTTP 요청를 처리하기 위한 시스템 기본 プロ키시로 이를 사용합니다.

### Linux/macOS

Linux 운영체제를 사용 중이라면 `/etc/environment` 파일을 업데이트하거나, macOS 사용자라면 홈 디렉터리에 있는 `.zshrc` 파일을 업데이트하십시오. 그런 다음 プロ키시 서버에 대한 새 항목을 추가하여 업데이트하십시오:

```
HTTP_PROXY=https://proxyserver:port
HTTPS_PROXY=https://proxyserver:port
```

터미널 세션 또는 시스템을 재시작하면 environment variables가 적용됩니다.

### Windows

Windows 시스템에서는 명령 프롬프트 터미널에서 다음 명령으로 environment variables를 설정할 수 있습니다:

```
setx HTTP_PROXY "https://proxyserver:port" /M
setx HTTPS_PROXY "https://proxyserver:port" /M
```

변경 사항이 적용되도록 명령 프롬프트를 재시작하십시오.

## Testing the Configuration

pip config file 또는 environment variables를 사용해 시스템 수준 구성을 설정한 후, プロ키시가 성공적으로 연결되고 데이터를 전송할 수 있는지 테스트하십시오.

### Linux/macOS

Linux/macOS에서는 다음 명령을 사용하십시오:

```bash
$ python -m venv venv
$ source venve/scripts/activate

# for pip config file or environment variables
(venv) $ pip install requests
```

특정 プロ키시로 이러한 설정을 재정의하고 싶다면, CLI 플래그를 사용하는 방식으로 돌아갈 수 있습니다:

```bash
# pip cli flag
(venv) $ pip install requests --proxy https://proxyserver:port
```

이 명령에서 `https://proxyserver:port`를 본인의 プロ키시로 업데이트해야 합니다.

### Windows

Windows에서는 다음 명령을 사용하십시오:

```powershell
> python -m venv venv
> .\venv\Scripts\Activate.bat
(venv) > pip install requests
```

이 설정은 pip CLI 플래그를 사용하여 언제든지 재정의할 수 있습니다:

```bash
# pip cli flag
(venv) $ pip install requests --proxy https://proxyserver:port
```

### Troubleshooting pip Proxies

pip로 HTTP 또는 HTTPS プロ키시에 연결할 때, 특히 더 강화된 기능 때문에 Private プロ키시 또는 HTTPS プロ키시 사용을 고려하는 경우 다음과 같은 일반적인 문제를 마주칠 수 있습니다.

#### Authentication Issues

인증 문제는 pip로 プロ키시에 연결하려 할 때 `407 Proxy Authentication Required` 오류로 흔히 나타납니다. 이는 プロ키시에 연결하기 위해 사용자 이름과 비밀번호가 필요하거나, プロ키시에 대해 잘못된 자격 증명을 제공했음을 의미합니다.

#### Certificate Issues

HTTPS プロ키시에 연결할 때 pip에서 `Certificate verify failed` 오류가 발생할 수 있습니다. 이는 プロ키시 서버가 제공한 인증서에 문제가 있음을 의미합니다.

Private プロ키시 서버가 자체 서명 인증서를 사용하는 경우, 인증 기관에 의해 인증서가 검증되지 않아 오류가 발생할 수 있습니다. 이를 우회하려면, 특정 도메인에 연결할 때 `--trusted-host` CLI 옵션을 사용하여 자체 서명 인증서 오류를 무시하는 것을 고려하십시오.

## Using pip with Rotating Proxies

[ローテーティングプロキ시](https://brightdata.co.kr/solutions/rotating-proxies)는 각 요청마다 IP 주소를 자동으로 전환하여 IP 차단을 피하는 데 도움이 됩니다. 이는 여러 사용자를 모방하고 제한을 우회합니다.

이는 목록에서 プロ키시를 무작위로 선택하는 방식으로 구현할 수 있습니다. 아래는 Public プロ키시를 순환하며 pip 패키지를 설치하는 간단한 bash 스크립트입니다.

다음 bash 스크립트를 `rotate-proxies.sh`라는 이름으로 생성하십시오:

```bash
proxy_list=(
  'http://45.185.162.203:999'
  'http://177.23.176.58:8080'
  'http://83.143.24.66:80'
)

pip_packages=(
  'requests'
  'numpy'
  'pandas'
)

# Loop through packages and install them
for package in "${pip_packages[@]}"
do
  # Randomly select a proxy from the list
  proxy=${proxy_list[$RANDOM % ${#proxy_list[@]}]}
  echo -e  "\nInstalling $package with proxy $proxy"
  pip install --proxy $proxy $package
done
```

파일을 만든 후 이를 실행하면, 각 pip 명령마다 무작위 プロ키시를 사용하여 pip 패키지를 다운로드할 수 있습니다. 아래는 스크립트 출력의 요약입니다:

```
$ ./rotate-proxies.sh 

Installing requests with proxy http://177.23.176.58:8080
Collecting requests
  Downloading requests-2.32.3-py3-none-any.whl.metadata (4.6 kB)

….

Downloading urllib3-2.3.0-py3-none-any.whl (128 kB)
Installing collected packages: urllib3, idna, charset-normalizer, certifi, requests
Successfully installed certifi-2025.1.31 charset-normalizer-3.4.1 idna-3.10 requests-2.32.3 urllib3-2.3.0

Installing six with proxy http://45.185.162.203:999
Collecting numpy
 Downloading numpy-2.2.2-cp313-cp313-macosx_14_0_x86_64.whl.metadata (62 kB)

…

Installing collected packages: numpy
Successfully installed numpy-2.2.2

Installing pandas with proxy http://83.143.24.66:80
Collecting pandas
  Downloading pandas-2.2.3-cp313-cp313-macosx_10_13_x86_64.whl.metadata (89 kB)

….

Installing collected packages: pytz, tzdata, six, python-dateutil, pandas
Successfully installed pandas-2.2.3 python-dateutil-2.9.0.post0 pytz-2025.1 six-1.17.0 tzdata-2025.1
```

## Benefits of Using Proxies with pip

プロ키시는 개발자가 네트워크 제한을 우회하고, 차단된 리소스에 접근하며, 패키지 다운로드 속도를 최적화할 수 있도록 합니다. Private プロ키시는 신원을 마스킹하여 보안을 강화하는 동시에 캐싱과 더 빠른 연결을 제공합니다.

개인정보 보호를 위해 모든 인터넷 트래픽을 암호화하지만 지연을 유발할 수 있는 [VPNs와 달리](https://brightdata.co.kr/blog/proxy-101/vpn-vs-proxy), プロ키시는 pip 요청에 대한 경량 대안으로 동작합니다. VPN의 성능 오버헤드 없이 더 빠르고 효율적으로 의존성을 관리할 수 있는 방법을 제공합니다.

## Common Mistakes and Best Practices

pip에서 プロ키시를 사용할 때는 보안 취약점으로 이어질 수 있는 일반적인 실수를 피하는 것이 중요합니다. 잘못된 プロ키시 URL이나, HTTP/HTTPS 프로토콜이 누락되거나 잘못된 형식의 주소와 같은 문제는 패키지 리포지토리로의 연결을 방해할 수 있습니다.

주요 보안 위험은 소스 코드, 스크립트 또는 CI/CD 파이프라인 구성에 プロ키시 자격 증명(예: 사용자 이름 및 비밀번호)을 하드코딩하는 것입니다. 코드가 공유되거나 노출되면 プロ키시에 대한 무단 접근이 발생할 수 있습니다. 유출된 자격 증명은 오남용으로 이어져 비용 증가 또는 사이버 공격에 의한 악용 가능성을 초래할 수 있습니다.

이러한 실수를 피하기 위해, プロ키시 자격 증명은 코드에 직접 포함하지 말고 environment variables 또는 암호화된 설정 파일에 저장하여 안전하게 관리하는 것이 권장됩니다. 또한, pip를 사용하기 전에 プロ키시 연결성을 테스트하여 올바른 설정을 보장하고 런타임 오류를 방지해야 합니다. [curl](https://curl.se/) 또는 [ping](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/ping) 같은 도구를 사용하면, 서비스에 투입하기 전에 プロ키시 성능을 검증할 수 있습니다. 이를 통해 더 원활한 패키지 관리 경험을 얻을 수 있습니다.

## Using Bright Data Proxies

Bright Data는 レジデンシャル, データセンタープロキ시, モバイル 장치를 포함한 다양한 [proxy services](https://brightdata.co.kr/proxy-types)를 제공합니다. 이를 통해 프로젝트 요구에 맞는 プロ키시를 쉽게 생성할 수 있습니다. pip에서 다른 IP 주소를 통해 패키지에 접근하는 데 사용할 수 있는 Private [residential proxy](https://brightdata.co.kr/proxy-types/residential-proxies)를 생성해 보겠습니다.

먼저 무료 Bright Data 계정에 가입하십시오. 그런 다음 [user dashboard](https://brightdata.co.kr/cp/start/)로 이동하십시오.

사이드 메뉴에서 **Proxies & Scraping**을 클릭하십시오:

![Bright Data proxies](https://github.com/bright-kr/pip-with-proxy/blob/main/Images/Bright-Data-proxies-2048x1041.png)

폼이 로드되면, 새 residential proxy를 설정하십시오. 기본 설정을 사용하면 여러 Bright Data 사용자가 사용하는 공유 IP 주소가 할당됩니다.

![Creating a residential proxy](https://github.com/bright-kr/pip-with-proxy/blob/main/Images/Creating-a-residential-proxy-2048x1040.png)

특정 지역의 IP 주소가 필요하다면, 설정 중 원하는 국가를 선택할 수 있습니다.

プロ키시가 생성되면, 엔드포인트 및 인증 세부 정보가 표시되는 대시보드로 리디렉션됩니다. 사용자 이름, 비밀번호, 서버 주소를 반드시 기록해 두십시오.

![Proxy dashboard](https://github.com/bright-kr/pip-with-proxy/blob/main/Images/Proxy-dashboard-2048x1040.png)

`--proxy` 플래그를 사용하여 엔드포인트 값의 가용성을 테스트하십시오:

```bash
$ pip install pandas \
    --trusted-host pypi.org \
    --trusted-host files.pythonhosted.org \
    --proxy https://username:[email protected]:33335
```

Bright Data プロ키시는 자체 서명 인증서를 사용하므로, `trusted-host` 플래그를 사용해 `pypi.org` 및 `files.pythonhosted.org`를 신뢰할 수 있는 도메인으로 화이트리스트에 추가할 수 있습니다.

## Conclusion

pip에 대한 プロ키시 구성은 CLI 플래그, pip config file, environment variables 등 여러 옵션으로 간단히 수행할 수 있습니다. 그러나 Public プロ키시는 한계가 있으며 대규모 워크로드나 프로덕션 사용에는 적합하지 않습니다. 더 신뢰할 수 있는 해결책을 위해 Bright Data는 レジデンシャル 및 [datacenter IPs](https://brightdata.co.kr/proxy-types/datacenter-proxies)를 제공하며, 빠르고 안정적인 연결과 Web스크레이핑 및 데이터 수집을 위한 고급 도구를 제공합니다. 무료로 가입하여 시작하십시오.