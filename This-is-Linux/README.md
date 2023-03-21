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
***

CentOS는 6개의 가상콘솔을 제공하며 가상의 모니터라고 생각하면 된다.

Ctrl+Alt+F1~F6으로 윈도우 전환

- Server에서 실습진행
    
    F2는 X윈도
    
    F3~F6은 일반 터미널화면 - 로그인가능
    
    root계정에서 shutdown시킬시 알림이 다른 윈도우에도 나타남
    

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

---

- 네트워크 개념
- nmtui
- DNS서버
- SELinux
- 파이프(|), 필터(|), 리디렉션 (>, <, >>, <<)

### 4.7 프로세스, 데몬, 서비스

---

### 4.8 서비스와 소켓

---

### 4.9 응급복구

---

### 4.10 GRUB 부트로더

---

### 4.11 커널컴파일

---
