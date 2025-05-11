# bullsbox
> Bull-strong and lightning-fast cloud file storage

Bullsbox is a project that starts with simple socket programming and gradually evolves into an enterprise-grade cloud storage service built on a scalable, extensible architecture.

### Lesson Learned
* Network programing(TCP/IP)
* I/O processing
* Multithreading
* Performance optimization
* Fault-tolarent, reliable system

### Constraints
* No language limits
* Protocol Standards (ex. FTP - [RFC Docs](http://proftpd.org/docs/rfc.html))
* Less external library / frameworks (Only Java API as possible)
* Step by step development
  * Executable Jars, Git(Use [Convetional commits rule](https://www.conventionalcommits.org/ko/v1.0.0/), [Git flow](https://devocean.sk.com/blog/techBoardDetail.do?ID=165571&boardType=techBlog))
  * Please submit documents such as architecture, api, deep dive, research, etc each steps...

### Additional Constaints
* Test converage (ex. Jacoco)
* Code quality (ex. SonarQube)
<br/><br/>

# MVP (Minimum Viable Product)
Simple FTP Server/Client Program

## Requirements
* **Funtional requirements**
  * 기본 FTP 명령어 지원(ex. USER, PASS, LIST, STOR, RETR, DELE...)
  * 요청에 대한 응답 코드 및 메시지 전송
  * 단일 사용자 인증 시스템
    * 메모리 기반 사용자/비밀번호 저장 (추후 파일 기반으로 확장)
    * 기본 인증 흐름: USER → PASS → 인증 성공/실패
  * 기본 파일 업로드/다운로드
  * 간단한 디렉토리 관리(MKD, RMD, CWD) - 디렉토리 생성/삭제, 파일 목록 조회 등
  * FTP Active/Passive Connection Mode
  * FTP Client 구현
    
* **Non-funtional requirements**
  * Intuitive CLI Env
  * Isolated file system management for security
  * Independence of Control and Data Flows(Only for FTP)
    * FTP separates the command channel (for control commands) from the data channel (for file transfer)
    * By using two connections, the client can still send control commands (e.g. pause, resume, change directory) over the command channel even while a large file is being transferred on the data channel.
  * Error Handling

## **생각해볼 점**
* Java IO vs NIO
* 사용자 인증
* 똑같은 파일을 계속 요청한다면?
* 업로드/다운로드한 파일과 원본과 동일한가?
<br/><br/>

# Bullsbox v1
## Requirements
* Funtional requirements
  * 동시 접속 지원
  * 다중 사용자 계층 지원
    * 사용자 권한(읽기/쓰기/삭제 등)
    * 사용자별 디렉토리 (각 클라이언트 세션 독립적 처리)
  * 파일 공유
  * 파일 메타데이터 관리

* Non-funtional requirements
  * 최소 100명 이상 동시 사용자 지원 - 동시성 제어 필요
  * 사용자별 접근 권한, 스토리지 할당량

## 생각해볼 점
* Thread Pool (+ JVM Platform Thread)
* Buffering Strategy
* Semapore
* Rate Limiter
* Load Balancer
* file metadata
<br/><br/>

# Bullsbox v2
* Funtional requirements
  * 대용량 파일 업로드/다운로드 지원
  * 전송 일시 중지/재개
  * 파일 청크 관리
  * 파일 무결성 검증
* Non-funtional requirements
  * 1GB 이상 파일 전송 속도 및 동시 업로드/다운로드 고려
  * 대용량 파일을 전송하다가 네트워크 상황으로 끊기면, 다시 처음부터 전송해야 하는가?
  * 사용자별 전송속도를 제어할 수 있을까? 만약 몇몇 사용자가 악성으로 계속 업로드/다운로드를 시도한다면?
  * 자원 관리
    * 네트워크 I/O 최적화
    * 버퍼 관리 및 메모리 최적
    * 유휴 연결 관리
    * 모니터링
  * FTP 프로토콜에 보안 계층이 생긴다면?

## 생각해볼 점
* TCP/IP Packet, Connection
* Java IO vs NIO
* QoS
* Reactor Pattern
* Session Checkpoint
* Retry with back-off
* Data compression
* Thread Pool (+ JVM Platform Thread)
* Thread Safety
* Resume upload/download
<br/><br/>

# Bullsbox v2 이후 아이디어
* 파일 복제 및 중복 제거
* 파일 버전 관리
* 자동 백업
* 클러스터링, Auto-scaling - 분산 락, 일관성 등 고려
* 접근 통제(Rate Limiter, IP Black/Whitelist 등)
* 분산 파일 시스템
* 스토리지 분리 및 다중화(로컬 FS, 객체 스토리지 사용 등)
* 인증 방식 변경(OAuth2 등), HTTP 프로토콜 등 프로토콜 다중화, CI/CD 등등...
* Web UI 지원

아키텍처 계층 관점에서는... 이런 느낌이 아닐까...
* Presentation
* Transport (TCP 소켓 / FTP 프로토콜 파서)
* Application (인증, API 처리, 세션 관리 등)
* Storage (파일 시스템 추상화, 메타데이터 관리)
* Infra (Thread Pool, I/O Model, 접근 제어, Security...)
