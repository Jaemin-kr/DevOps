# Ch.04 서버구축 필수 명령어

- poweroff, shutdow -P now, halt -p, init 0 (시스템 종료)
    
    ```bash
    shutdown -P +10 #10분후 power off
    shutdown -r 22:00 #22시에 reboot
    shutdown -c #shutdown cancel
    shutdown -k +15 #접속 사용자에게 15분후 종료된다는 메세지를 보내지만 실제로는 종료되지 않음
    ```
    
- shutdown -r now, reboot, init 6 (reboot)
- logout, exit (로그아웃)

### 가상콘솔

CentOS는 6개의 가상콘솔을 제공하며 가상의 모니터라고 생각하면 된다.

Ctrl+Alt+F1~F6으로 윈도우 전환

- Server에서 실습진행
    
    F2는 X윈도
    
    F3~F6은 일반 터미널화면 - 로그인가능
    
    root계정에서 shutdown시킬시 알림이 다른 윈도우에도 나타남
    

###다수의 사용자가 동시에 리눅스에 접속해 있을 때 시스템 종료 방식
2번 가상콘솔: root(F3)
3번 가상콘솔: centos(F4)

컨트롤 + 알트 + F3~6입력시 사용자 전환이 가능하다.

2번 가상콘솔 사용자: shutdown -h +5 입력(5분후 시스템 종료)
3번 가상콘솔 확인: 5분 후 시스템이 종료된다는 메시지가 전달됨 -> 5분동안 남은 작업 마무리 가능
2번 가상콘솔: shutdown -c 입력 -> shutdown 명령 취소
3번 가상콘솔: 시스템 종료가 취소되었다는 메시지가 

파일관리자 권한

sticky bit, setuid, setgid

하드링크 심볼릭링크

### 리눅스 관리자 명령어

- RPM(Redhat Package Manager)
- DNF(Dandified yum)
- 파일압축 및 풀기
- 파일 찾기
- 시스템 설정

### 4.5 네트워크 설정 및 명령어

- 네트워크 개념
- nmtui
- DNS서버
- SELinux
- 파이프(|), 필터(|), 리디렉션 (>, <, >>, <<)

### 4.7 프로세스, 데몬, 서비스

### 4.8 서비스와 소켓


### 4.9 응급복구


### 4.10 GRUB 부트로더


### 4.11 커널컴파일

