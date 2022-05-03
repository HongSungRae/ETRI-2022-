# ETRI - 2022 휴먼이해 인공지능 경진대회
> 본 대회는 한국전자통신연구원(ETRI)이 주최하고 과학기술정보통신부와 국가과학기술연구회(NST)가 후원합니다

## Abstract


## 1. 소개
### 1.1 대회 소개
#### 멀티모달 감정 데이터셋 활용 감정 인식 기술 분야
- 인간과 교감할 수 있는 인공지능 구현을 위해서는 인간의 행동과 감정을 이해하는 기술이 필요합니다. 이러한 기술 연구를 위해 구축한 데이터셋을 활용한 휴먼이해 인공지능 기술 연구를 위해 ETRI에서 금번 대회를 개최했습니다.

- Task는 다음과 같습니다.
    - 우리는 본 연구에서 성우 대상 상황극:[KEMDy19](https://nanum.etri.re.kr/share/kjnoh/KEMDy19?lang=ko_KR) 데이터셋을 활용하여 감정의 레이블(기쁨, 놀람, 분노, 중립, 혐오, 공포, 슬픔)에 대한 분류 정확도(F1)와 1~5단계의 각성도와 긍/부정도의 예측정확도(CCC;Concordance Correlation Coefficient)를 제시합니다.
    - 멀티모달 데이터를 혼합합니다. 발화음성과 발화텍스트를 사용하여 멀티모달 데이터 감정인식 모델을 구축했습니다.
    - 데이터 class 불균형 해소를 위한 Loss를 도입하고 그 효과를 비교했습니다.


### 1.2 Methodolgy
#### Model Architecture

#### Audio Spectrogram

#### Korean Sentence Embedding[1]

#### Class Balanced Loss[2]

## 2. How To Use?
- 이 코드를 사용하는 방법을 다룹니다
- 순서와 지시를 __그대로__ 따라 사용해주세요

### 2.1 환경설정
0. 여러분의 PC나 서버에 GPU가 있고 cuda setting이 되어있어야합니다.
1. 여러분의 환경에 이 repo를 clone합니다 : ```git clone <this_repo>```
2. requirements libraries를 확인합니다 : ```pip install -r requirements.txt```

### 2.2 데이터셋 다운로드
1. [KEMDy19](https://nanum.etri.re.kr/share/kjnoh/KEMDy19?lang=ko_KR) dataset을 다운로드하여 '2022-휴먼이해-인공지능-경진대회/data' 폴더에 넣으세요. 다운로드 권한을 신청해야할 수도 있습니다.
2. [Google_Drive]()에서 미리 가공된 sentence embedding과 annotation을 다운로드하여 '2022-휴먼이해-인공지능-경진대회/data/KEMDy19' 폴더에 넣으세요.

- 최종적으로 structure가 이렇게 되어있다면 모든 준비가 끝났습니다!
```
<2022-휴먼이행-인공지능-경진대회>
                            ├ <data>
                                └ <KEMDy19>
                                    ├ <annotation>
                                    ├ <ECG>
                                    ├ <EDA>
                                    ├ <TEMP>
                                    ├ <wav>
                                    ├ annotation.csv
                                    ├ df_listener.csv
                                    ├ df_speaker.csv
                                    └ embedding_768.npy
                            ├ constants.py
                            ├ dataset.py
                            ├ loss.py
                            ├ main.py
                            ├ metric.py
                            ├ model.py
                            ├ utils.py
                            ├ EDA.ipynb
                            ├ prerprocessing.ipynb
                            ├ LICENSE
                            └ README.md                           
```

### 2.3 학습+추론
#### Speaker 감정 추론 baseline
```
python main.py --SorL speaker
               --test_split [13,14,15,16]
               --lam 0.66
               --epochs 100
```

#### Listener 감정 추론 baseline
```
python main.py --SorL listener
               --test_split [13,14,15,16]
               --lam 0.66
               --epochs 100
```
- argparser parameter 소개
    - gup_id : 사용할 GPU의 id
    - save_path : 실험결과가 저장될 경로 -> 만지지 마시오
    - backbone : Backbone network -> 만지지 마시오
    - text_dim : sentence embedding의 dimension -> 만지지 마시오
    - bidirectional : 양방향 RNN옵션. Default는 False. True로 만드려면 ```--bidirectional```
    - ws : sliceing window size -> 만지지 마시오
    - SorL : 추론할 감정. ```speaker``` 또는 ```listener```
    - sr : audio의 sampling rate -> 만지지 마시오
    - test_split : 20개의 session중에서 test split으로 나눌 session. 예시) ```[1,8,9,13]```
    - batch_size : Batch size ```64```
    - optim : optimizer. choices=sgd,adam,adagrad
    - loss : Default는 ```normal```로 MSE와 KLDiv Loss를 사용합니다. ```cbloss```로 설정하면 Class Balanced Loss가 사용됩니다.
    - lam : Default는 ```0.66```으로 감정을 맞추는 가중치(lam)와 각성도,긍부정도를 맞추는 가중치(1-lam) 사이의 비율을 결정합니다.
    - beta : Default는 ```0.99```. CBLoss의 가중치 beta를 결정합니다.
    - lr_decay : lr decay term
    - lr : learning rate
    - weight_decay : weight decay term(L2 regularization)
    - epochs : total training epochs



### 2.4 추론만 하기
```
python main.py --test_only
               --./exp에있으면서_test할_모델이_있는_폴더_이름
```
예를 들어서 ```exp/lstm_speaker_adam_8/model.pth```가 있었고 이 모델을 테스트만 하고싶다면 아래와같이 명령하세요.
```
python main.py --test_only
               --lstm_speaker_adam_8
```

## 3. 성능


## Contact
- SungRae Hong : sun.hong@kaist.ac.kr
- TaeMi Kim : taemi_kim@kaist.ac.kr
- Sol Lee : leesol4553@kaist.ac.kr
- JongWoo Kim : gimjongu81@gmail.com

## Reference
[1]
[2]