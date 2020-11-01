# Object_Detection_RCNN
Object_Detection_RCNN

# R-CNN (Regions with CNN)

Rich feature hierarchies for accurate object detection and semantic segmentation (https://arxiv.org/pdf/1311.2524.pdf)

UC Berkeley에서 공개한 이 논문은 R-CNN 시리즈를 만들 정도로 물체 감지에 높은 인식률을 자랑합니다.

![img1](https://github.com/kjo26619/Object_Detection_RCNN/blob/main/R-CNN.PNG)

R-CNN의 전체 흐름입니다.

## Selective Search

R-CNN은 후보 영역을 찾기 위해 Selective Search를 사용합니다.

Selective Search는 Object Dectection을 위한 가능 후보 영역을 추천하는 방법으로, Segmentation 과 Exhaustive Search를 활용하여 효율적으로 후보 영역을 찾아내서 추천합니다.

Segmentation은 이미지의 색상, 무늬, 크기 등으로 후보 영역을 찾아내는 방법이고 Exhaustive Search는 가능한 모든 후보 영역을 찾아내는 방법입니다.

먼저, 이미지에서 Object가 될 수 잇는 다양한 영역을 모두 찾아냅니다. 이 때, Efficient Graph-Based Image Segmentation을 활용합니다.

그 후 각 영역들 간의 유사도를 계산해서 유사도가 높은 영역끼리 통합을 시킵니다. 비슷한 영역을 통합시킴으로써 Object 찾기를 더욱 쉽게 해줍니다.


# R-CNN Feature Extraction

R-CNN의 입력은 RGB 이미지를 정확하게 227x227로 리사이징 시켜서 입력을 합니다.

그리고 5개의 Convolution Layer와 2개의 Fully-Connected Layer를 통해서 총 4096 차원의 Feature Vector를 출력합니다.

## R-CNN Training

Selective Search를 이용하여 이미지에서 Object들이 있는 영역인 RoI(Region of Interest)를 추출해냅니다. 

모두 추출해냈으면 각 RoI들을 미리 ImageNet으로 학습시킨 CNN을 통과시킵니다.

Bounding Box Regression로 Bounding Box의 위치를 조정하고 SVM Classifier로 Object를 구별해냅니다.

## SVM Classifier

SVM은 Support Vector Machine을 뜻합니다. 

기계 학습 분야 중 패턴 인식에서 많이 사용되는 SVM은 두 카테고리 중 어느 하나에 속한 데이터의 집합이 나열되어 있을때, 이를 분류할 수 있는 모델을 만드는 방법입니다.

SVM은 d차원의 벡터가 주어지면, (d-1)차원의 Hyperplane을 가지고 분류하는 작업입니다.

당연하게도 여러 가지 Hyperplane이 나올 수 있고 SVM에서는 각 클래스의 점 중 Hyperplane 과의 가장 가까운 점의 마진이 가장 높게끔 선택합니다.

마진은 거리로 즉, Hyperplane과 가장 가까운 점의 거리가 최대가 되게끔 선택하는 것입니다. 이를 Maximum Margin Classifier라고 합니다.

R-CNN에서는 카테고리 별로 SVM으로 각 영역을 분류해냅니다.

## Bounding Box Regression

찾아낸 Bouding Box가 Object를 포함은 하나 정확하게 감싸지 않을 수 있습니다.

이러한 Bounding Box는 학습을 하는데 안좋게 작용할 수 있습니다. 그렇기에 Object를 중앙에 두고 전체적으로 감쌀 수 있도록 조정하는 작업이 Bounding Box Regression입니다.

Bounding Box Regression은 CNN 결과로 나온 Bounding Box P와 실제 값(Ground Truth) Bounding Box G와 비교를 해서 조정을 합니다.

Bounding Box는 x, y, width, height로 결과가 나옵니다. 즉, ![eq1](https://latex.codecogs.com/gif.latex?P%5Ei%3D%28P_x%5Ei%2C%20P_y%5Ei%2C%20P_w%5Ei%2C%20P_h%5Ei%29%20%5C%20%5C%20%5C%20G%5Ei%3D%28G_x%5Ei%2C%20G_y%5Ei%2C%20G_w%5Ei%2C%20G_h%5Ei%29) 로 나옵니다.

Bounding Box Regression을 수식으로 나타내면 다음과 같습니다.

![eq2](https://latex.codecogs.com/gif.latex?t_x%20%3D%20%28P_x-P_x%5E*%29/P^*_w%20%5C%20%5C%20%5C%20%5C%20%281%29)

![eq3](https://latex.codecogs.com/gif.latex?t_y%20%3D%20%28P_y-P_y%5E*%29/P^*_h%20%5C%20%5C%20%5C%20%5C%20%282%29)

![eq4](https://latex.codecogs.com/gif.latex?t_w%20%3D%20log%28P_w/P%5E*_w%29%20%5C%20%5C%20%5C%20%5C%20%283%29)

![eq5](https://latex.codecogs.com/gif.latex?t_h%20%3D%20log%28P_h/P%5E*_h%29%20%5C%20%5C%20%5C%20%5C%20%284%29)

![eq6](https://latex.codecogs.com/gif.latex?%5Chat%7Bt%7D_x%20%3D%20%28G_x-P%5E*_x%29/P_w%5E*%20%5C%20%5C%20%5C%20%5C%20%285%29)

![eq7](https://latex.codecogs.com/gif.latex?%5Chat%7Bt%7D_y%20%3D%20%28G_y-P%5E*_y%29/P_h%5E*%20%5C%20%5C%20%5C%20%5C%20%286%29)

![eq8](https://latex.codecogs.com/gif.latex?%5Chat%7Bt%7D_w%20%3D%20log%28G_w/P_w%5E*%29%20%5C%20%5C%20%5C%20%5C%20%287%29)

![eq9](https://latex.codecogs.com/gif.latex?%5Chat%7Bt%7D_h%20%3D%20log%28G_h/P_h%5E*%29%20%5C%20%5C%20%5C%20%5C%20%288%29)

여기서 ![pstar](https://latex.codecogs.com/gif.latex?P%5E*)는 조정한 Bounding Box입니다. 

Bouding Box를 조정해 실제 박스와 거의 유사하게 이동시킵니다.

# R-CNN NMS(Non-Maximum Suppression)

NMS는 각 RoI의 Object 검출 결과에서 후보군을 줄여 연산량을 줄이는 효과를 가져옵니다.

NMS를 위해서는 위해서는 먼저 IoU(Intersection over Union)를 계산해야 합니다.

IoU는 Jaccard Index라고도 불리는데, 전체 Union 박스에서 겹치는 부분을 나타내는 지표입니다. 겹치는 부분이 많을 수록 값이 높아집니다.

결국 NMS는 가장 높은 IoU를 가진 박스를 제외하고 모두 제거합니다.

R-CNN에서는 NMS를 IoU가 0.5 이상 크면 NMS를 적용합니다.

# R-CNN Speed

R-CNN은 높은 인식률을 가지고 왔지만 속도가 느리다는 단점이 있습니다.

그 이유는 각각 Object의 영역들을 뽑아내면 모든 영역에 대해서 CNN 작업을 통해서 분류합니다.

이러한 CNN 작업이 속도를 느리게 만들고 많은 연산을 하게 만듭니다.

이러한 방법을 개선한 것이 Fast R-CNN입니다.
