
# 목차
```
1. 노드 흐름 
2. 미완성인 부분
3. 주요 코드 위치
4. 빌드 방법
5. 실행 방법
6. 에러 해결책

```

### 1. 노드 정리


### 2. 미완성인 부분
```
1. 음원 cmake 2개인 문제, python으로 할껀지 cmake2개를 해결해서 할껀지 
2. 명령어가 너무 많기에 한번에 해주는 launch 파일 같은거 제작
3. 
```

### 3. 주요 코드 위치
```
라이다의 경우 압축을 풀면 됩니다  : $ unzip robot_4leg_robot.zip
라이다 데이터 pub                 : ros2_4leg_robot/src/ros2_robot_rider.py
받은 라이다 데이터를 움직임으로   : robot_connection-master/src/my_test_pkg_py/control.py
음원에서 나온 각도 pub            : Sound-source-localization-using-TDOA-master/final/main.cpp
받은 각도를 회전으로              : robot_connection-master/src/my_test_pkg_py/receive_angle.py
```

### 4. 빌드 방법
```
contorl 코드
$ cd robot_connection-master
$ colcon build --symlink-install --packages-select my_test_pkg_py
$ source install/setup.bash

라이다 
$ cd robot_4leg_robot
$ colcon build
$ source install/setup.bash

아직 미완
$ cd Sound-source-localization-using-TDOA-main
```

### 5. 실행 방법
```
1번 터미널 
$ ros2 launch rplidar_ros view_rplidar.launch.py

2번 터미널 
$ cd ros2_4leg_robot
$ python3 ros2_robot_ridar.py

3번 터미널
$ ros2 run my_test_pkg_py control

예상 출력값
```

### 6. 에러 해결책
1. 환경
2. ros2 launch rplidar_ros view_rplidar.launch.py 실행시 라이다 rviz 빨간점들이 보이지 않을때 
3. ros2 run my_test_pkg_py control 실행시 pt의 위치가 잘못 되었다고 나올때
4. 기타 


1. 환경 
```
듀얼 부팅으로 ubuntu 설치되어야함 
anaconda와 같이 기본 python 위치를 바꾸는 프로그램 제거 필요
```

2. ros2 launch rplidar_ros view_rplidar.launch.py 실행시 라이다 rviz 빨간점들이 보이지 않을때 
```
아래는 ttyUSB0의 읽고 쓸 권한을 부여한다. 

$ sudo chmod 777 /dev/ttyUSB0
안되면 777 대신 666써보자

아래는 ttyUSB0에 연결이 되어있는지 확인하는 코드이다. rplidar a1 package를 까보면 보이는데
여기선 default 값이 ttyUSB0를 기본으로 가져간다.

$ ls /dev/ttyUSB*
/dev/ttyUSB0가 나오면, 연결이 되어있다는 뜻이다.

포트가 충돌로 다른 프로세스가 사용하고 있다면, 연결이 되어있어도 작동이 안될수 있다.
$ sudo lsof /dev/ttyUSB0
위 과정은 현재 사용중인 프로세스를 보여준다. 만약 뭔가 있다면
$ sudo kill -9 <PID>
위 과정으로 제거한다.

권한의 마지막 과정이다. 위의 ttyUSB0를 쓸 권한을 부여했다.
본 사용자가 이 권한을 획득하는 것은 또 다른 문제인듯하다. 따라서 본 사용자가 권한이 있는지 확인해야한다.

$ ls -l /dev/ttyUSB0
crw-rw---- 1 root dialout 188, 0 ...
위의 코드는 포트에 대해 부여받은 권한이 있는지 확인하는 것이고, 윗줄의 주석처럼 결과가 나오면 성공이다

이제 본 사용자가 dialout 그룹에 포함되어있어야 본사용자가 ttyUSB0를 쓸수 있다.
$ groups $USER
이 코드의 결과로 dialout이 없다면,
$ sudo usermod -a -G dialout $USER
권한을 부여하면된다. 이후 bash shell 을 끈 후 다시 키면 된다.
```

3. ros2 run my_test_pkg_py control 실행시 pt의 위치가 잘못 되었다고 나올때
```
control.py의 146번째 줄의 /home/muwon/sound_and_test 부분을 본인에 맞게 수정

self.model.load_state_dict(torch.load('/home/muwon/sound_and_test/robot_connection-master/src/my_test_pkg_py/my_test_pkg_py/pt/4action_4_model.pt'))
```