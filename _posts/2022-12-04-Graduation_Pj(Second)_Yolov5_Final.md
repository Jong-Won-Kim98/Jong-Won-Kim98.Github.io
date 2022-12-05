---
layout: post
title: Parking space management
subtitle: Parking space management through image recognition
categories: Graduation_Pj(Second)
tags: YOLOv5
---

Yolov5��� ��ü �ν� ������ ��ũ�� ���� ������� �����غ��� ó�� ����� ���鼭 ó�� ��ȹ�ߴ� ������ ���� ���ߴ�. �켱 ù�� °�� ���� ����Ʈ���� ����Ϸ� �ߴ� �н� �������� ��� ��Ȯ�� ���鿡�� �ʹ� �������� �Ǵ��Ͽ� �ν��� ��ü�� ���� Label�� �������� �Ǵ��ϱ�� ����ߴ�. ������ ������ ����.

1. �׽�Ʈ �������� ���� ���������� �̹���ȭ
2. �̹����� ���� �������� ��ǥ�鿡 ���� ��ǥ �˻�
3. �̹������� ��ü �ν� �� �����Ǵ� exp ������ label �� ���
4. ��ü�� �ش� ��ǥ�� �νĵ� ��� ���� �Ұ��� �Ǵ�

### �׽�Ʈ �������� ���� ���������� �̹�ȭ

```Python
import cv2

cap = cv2.VideoCapture('C:/Graduation_Pj/yolov5-master/data/images/pest.mp4')  # ������ �ҷ�����
num = 0

while (cap.isOpened()):
    ret, frame = cap.read()
    if ret:
        cv2.imshow('frame', frame)
        # �̹����� �� �̸��� �ڵ����� ����
        path = 'C:/Graduation_Pj/yolo/final_pklot/yolov5-master/data/images/snapshot_661.jpg'
        cv2.imwrite(path, frame)  # ���� -> �̹����� ����
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break
    num += 1

cap.release()
cv2.destroyAllWindows()
```

- �̹���ȭ�� cv�� �̿��Ͽ���. �̸� ���� ������(640*640)�� �̿��� ���� �������� �̹����� �����Ͽ���.

<p align="center">
 <img src = "https://user-images.githubusercontent.com/77920565/205485185-f322d04a-2984-4c35-a5f0-5d3ebfdbac93.png" width = 380>
</p>

- ����� �� �׸��� ���� �⺻ ���� �̸� snapshot_num�� �����Ͽ� jpg ���Ϸ� ���� �Ǿ��� ���⼭ num�� �˻��غ� ��� 1�����ӿ� �ش��ϴ� ��� ������ �뷫 44���� ������ 660 �������̱� ������ 1�ʿ� 15�������� ������ �� �ִ�.

### �̹����� ���� �������� ��ǥ�鿡 ���� ��ǥ �˻�

```Python
import cv2

img = cv2.imread('C:/Graduation_Pj/yolo/final_pklot/yolov5-master/data/images/snapshot_650.jpg')

x_pos,y_pos,width,height = cv2.selectROI("location", img, False)
print("x position, y position : ",x_pos, y_pos)
print("width, height : ",width, height)

cv2.destroyAllWindows()
```
- ������ �ڸ� �Ѱ��� �̹����� ������ �����Ӱ� ���� �������̱� ������ �����󿡼� �ش��ϴ� ��ǥ��� ���ٰ� �� �� �ִ� ���� �Ʒ� �̹����� �������� �Ѱ��� �����忡 ���� ��� ��ǥ���� ���Ͽ���. 

<p align="center">
 <img src = "https://user-images.githubusercontent.com/77920565/205485325-33a94efd-e94f-4e0d-8cf3-6e8581d9366c.jpg" width = 380>
</p>

### �̹������� ��ü �ν� �� �����Ǵ� exp ������ label �� ���

<p align="center">
 <img src = "https://user-images.githubusercontent.com/77920565/205485389-0a29229f-6e27-4375-8576-0adb82c638a4.png" width = 380>
</p>

- Yolov5�� detect ������ ������ �� --save-txt �ɼ��� �߰����� ��� �� �׸��� ���� runs/dtext/exp/labels��� ������ ���Ⱑ �̹����� �̸��� �Բ� txt ������ �����ȴ�.

<p align="center">
 <img src = "https://user-images.githubusercontent.com/77920565/205485447-9e0602c3-28e6-4012-8f36-2b2ed7c2bbe6.png" width = 380>
</p>

- txt ������ ������ ������ ���� ������ ������� class �̸�, ��ü�� ���� x��ǥ, y��ǥ, ����, ���� ������ ����ȴ�. �츮�� �� �����Ǵ� label�� ������ �̿��Ͽ���.

### ���� �ڵ�

Yolov5�� detect ���ϰ� �̸� �̿��� ��� ���� label�� �̿��Ͽ� ��ü�� �ν��ϰ� API���� ����ȭ �ϱ� ���� Firebase���� �����ϴ� Realtime database�� �̿��Ͽ� �����ϰ� �̸� �ǽð����� �ְ�޾Ҵ�.

```Python
import os
import sys
import time
import firebase_admin
from firebase_admin import credentials
from firebase_admin import db

tmp_count_filename = 0
tmp_count_exp = 2
img_source_original = "snapshot_"
tmp_reading_txt = " "
count_occupied = 0
count_empty = 0
cred = credentials.Certificate(
        "C:/Graduation_Pj/yolo/final_pklot/yolov5-master/yolov5-f7e84-firebase-adminsdk-7dbnc-6e25b09e81.json")
firebase_admin.initialize_app(cred, {
        'databaseURL': 'Firebase ���� ���'
})
ref = db.reference()

sys.stdout = open('output1.txt', 'at')

while tmp_count_filename < 663:

    print("filename : ", tmp_count_filename)

    img_name = img_source_original + str(tmp_count_filename)

    tmp_command = 'python detect.py --img 640 --weight yolov5x.pt --source "data/images/' + img_name + '.jpg" --view-img --save-txt --line-thickness 2 --hide-labels --hide-conf'

    os.system(tmp_command)
    #time.sleep(5)

    tmp_reading_txt = "runs/detect/exp" + str(tmp_count_exp) + "/labels/" + img_name + ".txt"
    original_txt = open(tmp_reading_txt, 'r')

    data_tmp = original_txt.read().splitlines()
    data_list = []
    tmp_list = []
    tmp_list2 = []
    SPOT_list = [0 for i in range(12)]
    final_list = []
    final_list_width = []
    tmp = 0

    for i in range(len(data_tmp)):
        tmp_list = data_tmp[i]
        data_list.append(data_tmp[i])

    for i in range(0, len(data_tmp), 1):
        tmp_list2 = data_list[i].split(" ")
        for j in range(1, 3, 1):
            tmp = round(float(tmp_list2[j]) * 640)
            final_list.append(tmp)

    for i in range(0, len(final_list), 2):
        # SPOT1
        if final_list[i] in range(282, 360) and final_list[i + 1] in range(323, 375):
            print("SPOT1 occupied")
            SPOT_list[0] = 1

        # spot2
        elif final_list[i] in range(357, 428) and final_list[i + 1] in range(321, 372):
            print("SPOT2 occupied")
            SPOT_list[1] = 1

        # spot3
        elif final_list[i] in range(357, 505) and final_list[i + 1] in range(319, 372):
            print("SPOT3 occupied")
            SPOT_list[2] = 1

        # spot4
        elif final_list[i] in range(485, 853) and final_list[i + 1] in range(319, 370):
            print("SPOT4 occupied")
            SPOT_list[3] = 1

        # spot5
        elif final_list[i] in range(552, 639) and final_list[i + 1] in range(317, 368):
            print("SPOT5 occupied")
            SPOT_list[4] = 1

        # spot6
        elif final_list[i] in range(70, 150) and final_list[i + 1] in range(427, 477):
            print("SPOT6 occupied")
            SPOT_list[5] = 1

        elif final_list[i] in range(131, 264) and final_list[i + 1] in range(408, 498):
            print("SPOT7 occupied")
            SPOT_list[6] = 1

        # spot8
        elif final_list[i] in range(231, 344) and final_list[i + 1] in range(407, 498):
            print("SPOT8 occupied")
            SPOT_list[7] = 1

        elif final_list[i] in range(332, 434) and final_list[i + 1] in range(407, 496):
            print("SPOT9 occupied")
            SPOT_list[8] = 1

        # spot10
        elif final_list[i] in range(451, 518) and final_list[i + 1] in range(430, 475):
            print("SPOT10 occupied")
            SPOT_list[9] = 1

        # spot11
        elif final_list[i] in range(511, 637) and final_list[i + 1] in range(404, 500):
            print("SPOT11 occupied")
            SPOT_list[10] = 1

    for i in range(0, 12, 1):
        if int(SPOT_list[i]) == 1:
            count_occupied += 1
        else:
            count_empty += 1
        print("SPOT", i + 1, ":", int(SPOT_list[i]))

    print("Occupied : ", count_occupied)
    print("Empty : ", count_empty, "\n")

    #�ʱ�ȭ
    count_occupied = 0
    count_empty = 0

    tmp_count_filename += 65
    # 1��: 15
    tmp_count_exp += 1
    dictionary = {(i+1): str(SPOT_list[i]) for i in range(0, len(SPOT_list))}
    print(dictionary)
    #with open('C:/Graduation_Pj/yolo/final_pklot/yolov5-master/data/persons.json', 'w') as f:
    #    json.dump(dictionary, f, indent=4)
    ref = db.reference('pklot/test1')
    ref.update(dictionary)
sys.stdout.close()
```

- �⺻���� ���α׷� ���� �ֱ�� ���� �̹����� ���� �⺻ �̸�, filename, label�� txt ������ �б� ���� ����, ���� ������ ���� occupied, empty ���� ����, firebase�� �����ϱ� ���� url�� json ������ ������ �־���.
- �� �������� �̹����� ���� 660�� �̱� ������ while �ݺ����� �̿��Ͽ� �ý����� ���� �� �� �ֵ��� �Ͽ���.
- os.system()�Լ��� �̿��� Yolov5�� detect.py ������ ���� ���װ� �ѹ� ������ ������ ������ ������ exp ������ �����ǰ� �� �ؿ� �ִ� label ������ txt ���Ϸ� �����Ͽ���
- final_list�� �츮�� ������ label�� txt ���Ͽ��� �ν��� ��ü�� ���� x, y �߽� ��ǥ�� ������ �������� �� ��ü�� ���� 2���� ��ǥ�� �ֱ� ������ 2�� �ֱ�� for �ݺ����� ���� ���״�.
- final_list�� ����Ǿ� �ִ� �ν��� ��ü�� ���� ��ǥ�� ��ŭ �ش� ������ ��ǥ�� ��ü�� �νĵ� ��� 0���� �ʱ�ȭ �Ǿ��ִ� SPOT_list[]�� ���� 1�� �����Ͽ� �ڵ����� �̹� �����Ǿ� ������ �Ǵ��Ͽ��� occupied ������ 1�� �������� �־���.
- ������ �� ��� ��(SPOT_list[])�� Json ���Ϸ� �����ϱ� ���� dictionary�� �����Ͽ� �̸� �����Ͽ����� firebase�� �ǽð����� �����ϱ� ���� �ٷ� �����Ͽ� �����Ͽ���. 

<p align="center">
 <img src = "https://user-images.githubusercontent.com/77920565/205486030-eb818887-46d7-40b0-8754-f2dd0f477a2f.png" width = 700>
</p>

- �׸��� ���� ������ ���� ���� ���� �ǽð����� firbase�� ���� ���ϴ� ���� �� �� �־���. ���� �׽�Ʈ ������ �ƴ� ���� ���� ������ �������� ���� ������ ��ȭ�� �ν��Ͽ� �����ؼ� �װ��� APP���� ����ȭ�غ� �����̴�.
