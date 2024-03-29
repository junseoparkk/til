# 리눅스 기초 명령어

### 1. 사용자 인증 관련

```bash
# 사용자 인증 로그인
login

# 사용자 패스워드 변경
passwd

# 현재 시스템에 로그인한 사용자 리스트
who
```
---

### 2. 파일 관련

```bash
# 현재 디렉토리 파일 보기
ls
```

#### 옵션
- F : 파일 유형 기호 표시 (디렉토리 : '/', 실행파일 : '*', 링크 : '@')
- l : 파일 상세 정보 표시
- a : dot 파일을 포함한 모든 파일 표시
- C : 한 줄에 여러 개 정보 표시
- R : 서브 디렉토리 내용까지 표시
<br><br>

```bash
# 파일 생성
cat > <파일명>
touch <파일명>
vi <파일명>

# 파일 내용 출력
cat <파일명>
head -n <파일명>
tail -n <파일명>
more <옵션> <파일명>

# 파일 이동
mv <파일명> <위치>

# 파일 이름 변경
mv <파일명> <새로운 파일명>

# 파일 복사
cp <파일명> <복사할 파일명>
cp <파일명> <복사할 디렉토리명>

# 파일 삭제
rm <파일명>

# 파일 실행
./<파일명>
```
---

### 3. 디렉토리 관련
#### 1) 디렉토리 이동
```bash
# 디렉토리 이동
cd <디렉토리>

# 현재 디렉토리의 상위 디렉토리로 이동
cd ..
cd ../
cd /..

# 홈 디렉토리로 이동
cd ~

# 경로를 통해 디렉토리 이동
cd <디렉토리1>/<디렉토리2>/<디렉토리3>
```

#### 2) 디렉토리 관리
```bash
# 디렉토리 생성
mkdir <디렉토리명>

# 디렉토리 삭제 (하위도 모두 삭제, 해당 디렉토리만 삭제)
rm -r <디렉토리명>
rmdir <디렉토리명>

# 현재 디렉토리 경로 확인
pwd
```
---
### 4. 사용자 권한 관련

각 파일과 디렉토리에 사용 권한을 부여할 수 있다. <br>

- 읽기(read) : 파일 읽기 권한
- 쓰기(write) : 파일 쓰기 권한
- 실행(execution) : 파일 실행 권한
- 없음(-) : 사용 권한 없음
<br><br>

읽기는 (4), 쓰기는 (2), 실행은 (1) 을 부여하여 권한을 나타냄<br>
```bash
# 예시
#(사용자 자신의 권한)(그룹 사용자의 권한)(전체 사용자의 권한)
-rwxr-xr-x index.html
```
<br>

- 처음 3개 문자 (rwx) : 사용자 자신에게 읽기, 쓰기, 실행 권한 부여
- 중간 3개 문자 (r-x) : 그룹 사용자에게 읽기, 실행 권한 부여
- 끝 3개 문자 (r-x) : 전체 사용자에게 읽기, 실행 권한 부여
<br><br>

이를 4, 2, 1의 숫자를 합해 나타낸다.

```bash
# 사용자 권한 변경
chmod <변경모드> <파일>

# 예시1 : 자신은 모든 권한을,그룹 사용자와 전체 사용자는 읽기, 실행 권한만 줌
chmod 755 index.html

# 예시2 : 자신을 포함한 모든 사용자에게 읽기, 쓰기, 실행 권한을 줌
chmod 777 index.html
```
---

### 5. 파일 압축 관련
```bash
# .tar, _tar로 된 파일을 압축하거나 압축 해제할 때 사용
tar

# 각각 압축할 때와 압축 해제할 때
tar cvf <파일명.tar/_tar>
tar xvf <파일명.tar/_tar>

# .Z 형태의 압축 파일 생성, 각각 압축할 때와 해제할 때
compress <파일명>
uncompress <파일명>

# .gz, .z 형태의 압축 파일 생성, 각각 압축할 때와 해제할 떄
gzip <파일명>
gzip -d <파일명>
```