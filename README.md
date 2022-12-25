![header](https://capsule-render.vercel.app/api?type=waving&color=auto&height=200&text=Welcome!%20&fontSize=60&fontAlignY=40&desc=I'm%20joonho)
##### 팀원 : 마준호 <br/>프로젝트 수행기간 : 1주일<br/><br/><br/>

# Apple_classification
* Task 목적 : EfficineNet 모델을 사용하여, 사과종류 분류하기
* 개인목적 : optuna, wandb, Image augmentation 의 활용
* 결과 : Image augmentation 을 활용한 결과 f1score가 0.94 -> 0.97 로 향상
<br/>
  
  

## EfficienNet B4 란?
#### Channel(Width), Layer(Depth), Input size(Resolution) 세가지 방법을 종합하며 모델을 확장시키는 방법
![image](https://user-images.githubusercontent.com/103080228/204213819-e471b4af-d690-4a32-82e8-72c79ea6ea91.png)

### Compound Model Scaling
* Depth : Network가 깊어지게되면 좀 더 풍부하고 복잡한 특징을 추출할 수 있음. 하지만 Depth가 깊어질수록 vanishing gradient 현상이 생기게 되어 성능이 저하됨<br/>이를 위해 skip connection, batch normalization 등의 방법들이 있지만 너무 깊은 Layer를 가진 모델의 성능은 더 좋아지지 않음.

* Width : 넓은 Width(Channel)은 더 세밀한 특징을 추출할 수 있음. 

* Resolution : 높은 해상도의 이미지를 input으로 사용할 때 모델은 더욱 세밀한 패턴을 학습할 수 있음.<br/> --> 성능을 높이기 위해서 Resolution을 크게 가져가고 있음.

![image](https://user-images.githubusercontent.com/103080228/204216545-0afc2252-21c0-4a6a-8616-b28e20595856.png)
* 그래프에서 알 수 있듯이 몇년 지난 모델임에도 최신 모델보다 뛰어나며, parameter 수가 적어 가볍게 수행할 수 있음.<br/>--> 단 성능을 높이기 위해서는 b0보다는 b4를 사용하는 것을 권장함.

[EfficienNet참조](https://visionhong.tistory.com/19)
<br/><br/><br/>

## Image augmentation

![image](https://user-images.githubusercontent.com/103080228/204218063-8fbeb6e2-ff6e-4ea0-946b-6901bca71c7a.png)

* Augmentor 라이브러리를 이용

![image](https://user-images.githubusercontent.com/103080228/204218419-fbc5abde-eaa6-408e-91c1-106c2e319cca.png)

* 이미지데이터 불균형을 해결하기 위해 부족한 이미지를 augmentation 실행

![image](https://user-images.githubusercontent.com/103080228/204218739-748bd06b-9906-4709-b5d0-ac3969e003fe.png)

* 좌우반전, 상하반전, 회전, random_distortion 적용. (2000 장씩)
* 색상은 사과이미지에 치명적인 오류발생 가능성이 있으므로 적용하지 않음.

![image](https://user-images.githubusercontent.com/103080228/204219059-cf6a9ae5-a8c1-48cc-bf3f-67072ae2c8fc.png)

* 새로 생성된 Image 에 label 만들어 traindf 와 concat.
* `p = Augmentor.Pipeline("/path/to/images")` : mask가 없는 image
* `p = Augmentor.DataPipeline(images, y)` : mask가 있는 image
* 최종 14,000 장의 Image Data 확보
* 참조 : https://github.com/mdbloice/Augmentor
<br/><br/><br/>

## optuna, wandb

![image](https://user-images.githubusercontent.com/103080228/204221791-abf5c2f5-890e-4f0a-bf10-100ea2b9208d.png)

* 모델 정의(EffeNet)
* 모델을 상속받아 할 경우에 모델에도 trial 을 만들어 줘야함.
* out_features, dropout 을 search 하도록 하였음.

![image](https://user-images.githubusercontent.com/103080228/204223163-07a0093b-38fb-4779-be3c-b5db83b52df7.png)

* learning rate 와 optmizer 를 search 하도록 하고 동시에 wandb 설정을 하였음.

![image](https://user-images.githubusercontent.com/103080228/204223777-4dd04e17-8b10-4214-855c-a83095af5f9d.png)

* wandb 로 데이터를 보낼 수 있도록 설정하였음.

![image](https://user-images.githubusercontent.com/103080228/204224092-b566f873-cb53-4728-b6f3-2f65d6f3c32b.png)

* f1score 가 커지는 방향으로 학습이 되어야 하므로, direction 은 maximize 로 설정함.

![image](https://user-images.githubusercontent.com/103080228/204224415-86e7c480-8459-45a4-97c4-41633d66667a.png)
![image](https://user-images.githubusercontent.com/103080228/204224802-b7742c0e-d40f-4a6d-a80d-8aa291f304de.png)

* optuna 와 wandb 를 동시에 적용한 결과, 더 쉽게 학습결과를 저장하고 비교할 수 있음.
* 단순 결과 뿐만 아니라 system 자원활용 정도까지 알 수 있어서 다양한 측면의 비교가 가능함.
* optuna 를 활용하는 것도 좋지만, 결국에는 Image augmentation 같은 방법을 활용하여 데이터의 불균형을 해소하고 다양한 경우의 수를 만드는 것이 더 중요함.
<br/><br/><br/>

## result
![image](https://user-images.githubusercontent.com/103080228/204226256-5ad0e119-7253-47f7-a7f6-6e39adedee91.png)
![image](https://user-images.githubusercontent.com/103080228/204226648-65f011e1-28db-4b9f-a1f7-06de4177a458.png)
* 19등/137명
<br/><br/><br/>

## Todo
* GPU를 계속 사용할 수 없어서 다양한 작업에 한계가 있어 아쉬웠음
* 데이터불균형을 위한 task 뿐만 아니라 다양성을 위한 task 추가
* 다른 augmentation 추가를 하면 성능이 더 향상 될 것으로 예상됨






















