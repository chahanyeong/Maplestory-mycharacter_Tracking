# 개발내용
보스레이드하는 나의 캐릭터를 추적하는 알고리즘 개발

https://github.com/chahanyeong/Maplestory-mycharacter_Tracking/assets/152364900/8929d1bc-eb34-48ae-9af0-8e60f441aa20

# 개발순서
- Roboflow를 사용한 Create Data, labeling(Bounding box)
- Colab에서 API를 사용해 Roboflow Data Download
- YOLOv8 Fine-Tuning
- YOLOv8 + Re-ID + BOTSORT 사용한 나만의 캐릭터 Tracking

# 데이터 구축
제 캐릭터는 LV278 나이트 워커입니다. (최근에 재미가 없어져서 친구와 같이 보스를 다니는 비숍으로 본캐를 바꾸고 이건 유기했습니다... >.<)
아래는 나이트 워커가 가지고 있는 대표적인 모션 6가지입니다. 

![모션들](https://github.com/chahanyeong/Maplestory-mycharacter_Tracking/assets/152364900/b1dd59cd-bb64-49a9-a751-8d9e4e5a7053)

이런 여러가지 모션들을 캡처한 후 Roboflow를 사용해 데이터를 구축하고 라벨링을 진행하였습니다.

![데이터 구축 사진](https://github.com/chahanyeong/Maplestory-mycharacter_Tracking/assets/152364900/aa922266-806b-4772-8965-4805465598ea)

이때 아래의 사진들과 같이 데미지 스킨, 펫, 보스에 의한 구조물들에 의해 캐릭터가 일부 가려지는 경우도 많았습니다. 따라서 데이터 생성시 Crop, Noise, 좌우반전 등의 Augmentation을 수행해 YOLOv8의 Detecting 능률을 올렸습니다.

![노이즈 예시](https://github.com/chahanyeong/Maplestory-mycharacter_Tracking/assets/152364900/6bf9c5f1-5e63-4154-b97a-9136bb432585)

RoboFlow에서 구축한 데이터는 아래의 코드를 사용하여 Colab 환경에 다운로드 할 수 있습니다.
```bash
!mkdir {HOME}/datasets
%cd {HOME}/datasets

!pip install roboflow --quiet

from roboflow import Roboflow
rf = Roboflow(api_key="YOUR_API_KEY")
project = rf.workspace("roboflow-jvuqo").project("maplestory-1v")
dataset = project.version(1).download("yolov8")
``` 

# YOLOv8 Fine-Tuning
YOLOv8모델의 Fine-Tuning 결과와 실제 detection 사진입니다. validation set에서도 detecting이 잘 진행되고 있음을 볼 수 있습니다.

![훈련결과](https://github.com/chahanyeong/Maplestory-mycharacter_Tracking/assets/152364900/7224ac2a-acf2-47cf-90ca-1de7d2fb5cab)

![validset 이미지](https://github.com/chahanyeong/Maplestory-mycharacter_Tracking/assets/152364900/88727436-1b98-4a9f-8b83-52de2a1b6076)

# Tracking
Tracking 알고리즘은 BOTSORT를 사용하였고 객체가 사라졌다 나타날 때 같은 ID로 추적하도록 유도하는 RE-ID 모델도 사용하였습니다.
![image](https://github.com/chahanyeong/Maplestory-mycharacter_Tracking/assets/152364900/fedba7f0-93f4-4af8-8061-664375a81e7c)

