# Malicious-comment-detection
악성댓글 탐지 및 정확도 측정

Jigsaw Toxic Comment Classification Challenge를 위한 RNN 모델을 구현한 
코드에 대해 분석하고 설명하는 것을 목적으로 합니다.
유해한 댓글과 그렇지 않은 댓글이 포함된 데이터 세트에서 모델을 학습하고, 새로운 댓글
의 유해성을 예측합니다
#RNN을 서택한 RNN의 장점(RNN모델을 선택한 근거들)
순서 정보 학습:
 • RNN은 입력 데이터의 순서를 고려하여 학습할 수 있습니다. 이는 시계열 
데이터나 자연어 처리와 같은 순서 또는 시퀀스가 중요한 데이터에 특히 유
용합니다.
변동적인 길이의 입력 처리:
 • RNN은 입력 시퀀스의 길이가 가변적일 때도 사용할 수 있습니다. 시퀀스의 
길이에 따라 가변적으로 펼쳐지므로, 다양한 길이의 시퀀스에 대응할 수 있
습니다.
#파라미터 공유:
 • RNN은 같은 네트워크의 가중치를 각 시간 단계에서 재사용함으로써 모델
의 파라미터를 공유할 수 있습니다. 이는 모델의 학습 파라미터를 줄일 수 
있어 계산 비용을 절감하고 효율성을 높일 수 있습니다.
#계층적 패턴 학습:
 • RNN은 계층적인 패턴을 학습할 수 있습니다. 모델은 이전 시간 단계의 정
보를 사용하여 현재 시간 단계의 출력을 생성하므로, 긴 시퀀스에서도 전체
적인 패턴을 파악할 수 있습니다.
#메모리 활용:
 • RNN은 내부 상태(은닉 상태)를 통해 이전 시간 단계의 정보를 메모리에 저
장하고 활용할 수 있습니다. 이를 통해 모델은 더 오랜 기간 동안의 의존성
을 파악할 수 있습니다.
#자연어 처리에 적합:
 • RNN은 특히 자연어 처리 작업에 적합하며, 문장의 문맥을 이해하고 의미론
적인 표현을 학습하는 데 강점을 가지고 있습니다.

#다른 모델을 고려했을 경우의 단점
1. WORD2VEC을 사용할 경우-임베딩의 크기가 고정되어 있어 특정 작업에 최적화된 임베딩 크기를 학습 불가-특정 도메인에서 새로운 단어가 나타날 때 문제가 될수 있음
2.MLP을 사용할 경우-시퀀스 정보를 완전히 무시하므로 순차적인 데이터에 대해 부적합-전역 패턴을 학습하기 떄문에 지역적인 특징을 놓칠수 있음
악성 댓글 탐지를 위한 분류 모델

#데이터 전처리:
 • torchtext를 사용하여 CSV 파일에서 텍스트 데이터를 로드하고, 이를 훈련, 
검증, 테스트 세트로 분할합니다.
 • 텍스트 데이터는 SpaCy 토크나이저를 사용하여 토큰화됩니다.
어휘 사전 생성 및 임베딩:
 • 텍스트 데이터의 어휘 사전을 생성하고, 이를 기반으로 임베딩을 수행합니
다.
 • torchtext의 Field 클래스를 사용하여 텍스트와 라벨을 처리하고, 어휘를 구
축합니다.
 
#RNN 모델 정의:
 • 간단한 순환 신경망(RNN) 모델을 정의합니다. 이 모델은 임베딩된 텍스트를 
입력으로 받아 순차적으로 처리하고 최종적으로 이진 분류를 수행합니다.
모델 학습 및 검증:
 • SGD(Stochastic Gradient Descent)를 사용하여 모델을 훈련하고, 검증 데이
터를 사용하여 모델의 성능을 평가합니다.
 • BCEWithLogitsLoss를 사용하여 이진 분류에 적합한 손실을 계산합니다.
최적 모델 저장:
 • 검증 세트에서 최적의 성능을 보이는 모델을 저장합니다. 이는 모델 성능이 
개선되는 경우에만 수행됩니다.
최종 테스트 및 제출용 파일 생성:
 • 저장된 최적 모델을 로드하고, 테스트 데이터를 사용하여 최종 모델 성능을 
평가합니다.
 • 테스트 데이터에 대한 예측 결과를 CSV 파일로 저장하여 제출용 파일을 생
성합니다.

#코드 분석
코드는 다음과 같은 구조로 되어 있습니다.
 • 데이터 전처리: 데이터를 로드하고, 사전을 생성하고, 배치를 정렬합니다.
 • 모델 학습: 모델을 정의하고, 최적화기와 손실 함수를 정의하고, 훈련 루프를 구현
합니다.
 • 테스트 및 제출: 모델을 평가하고, 예측을 생성하고, 제출 파일을 생성합니다.

#데이터 전처리
데이터 전처리 단계에서는 다음 작업을 수행합니다.
 • Pandas를 사용하여 데이터를 CSV 파일에서 로드합니다.
 Pandas는 Python의 데이터 분석 라이브러리입니다. 이 코드에서는 Pandas를 사용하여 데이
터를 CSV 파일에서 로드합니다. CSV 파일은 다음과 같은 형식으로 되어 있습니다.
 comment,toxicity
 "This is a toxic comment.",1
 "This is a non-toxic comment.",0
 • TorchText를 사용하여 댓글과 유해성 레이블에 대한 사전을 생성합니다.
 TorchText는 PyTorch의 자연어 처리 라이브러리입니다. 이 코드에서는 TorchText를 사용하
여 댓글과 유해성 레이블에 대한 사전을 생성합니다. 사전은 텍스트를 숫자로 인코딩하는 
데 사용됩니다.
 • TorchText BucketIterator를 사용하여 배치를 효율적으로 정렬합니다.
 TorchText BucketIterator는 배치를 효율적으로 정렬하는 데 사용됩니다. 이 코드에서는 
TorchText BucketIterator를 사용하여 댓글 길이에 따라 배치를 정렬합니다.

#모델 학습
모델 학습 단계에서는 다음 작업을 수행합니다.
 • RNN 모델을 정의합니다.
 RNN 모델은 다음과 같은 구조로 되어 있습니다.
 Embedding Layer(input_dim, embedding_dim)
 RNN Layer(embedding_dim, hidden_dim)
 Linear Layer(hidden_dim, output_dim)
 Embedding Layer는 텍스트를 숫자로 인코딩합니다. RNN Layer는 텍스트의 순서를 고려합
니다. Linear Layer는 텍스트의 유해성을 예측합니다.
 • SGD 최적화기와 BCEWithLogitsLoss 손실 함수를 사용합니다.
 SGD 최적화기는 Stochastic Gradient Descent의 약자로, 경사하강법의 일종입니다. 
BCEWithLogitsLoss 손실 함수는 이진 분류 문제에 사용됩니다.
 • 훈련 루프를 구현합니다.
훈련 루프는 다음 단계를 수행합니다.
 * 기울기를 0으로 설정합니다.
 * 각 댓글의 유해성을 예측합니다.
 * 손실과 정확도를 계산합니다.
 * 손실을 역전파합니다.
 * 최적화기로 가중치를 업데이트합니다.
 * 에폭 손실과 정확도를 추적합니다.

#테스트 
테스트 단계에서는 다음 작업을 수행합니다.
 • 저장된 최상의 모델을 로드합니다.
훈련 과정에서 저장된 최상의 모델을 로드합니다.
 • 테스트 데이터에 대해 모델을 평가합니다.
테스트 데이터에 대해 모델의 정확도, F1 점수, ROC AUC 점수를 계산합니다.
 • 테스트 데이터에 대한 예측을 생성합니다.
테스트 데이터에 대한 예측을 생성합니다.
 • 제출 파일을 생성합니다.
이 코드는 훈련된 모델의 각 에포크에 대한 훈련 및 검증 결과를 보여줍니다. 주요 지표로
는 F1 Score 및 ROC AUC Score가 사용되었습니다. 이러한 지표는 주로 이진 분류 모델의 
성능을 평가하는 데 사용됩니다.
 F1 Score (F1 점수):
 • F1 점수는 모델의 정밀도(Precision)와 재현율(Recall)의 조화 평균입니다.
 • 높은 F1 점수는 모델이 클래스를 정확하게 식별하고 있으며, 정밀도와 재현
율 간의 균형을 나타냅니다.
 • 에포크 1부터 5까지 F1 점수는 각각 0.124, 0.108, 0.097, 0.097, 0.092입니
다.
 
#ROC AUC Score:
• ROC AUC(Receiver Operating Characteristic Area Under the Curve)는 모델
의 분류 성능을 나타내는 지표 중 하나입니다.
 • 0에서 1까지의 값을 가지며, 1에 가까울수록 모델의 성능이 우수합니다.
 • 에포크 1부터 5까지 ROC AUC 점수는 각각 0.551, 0.559, 0.564, 0.567, 
0.568입니다.
해석:
 • 훈련 및 검증 손실은 에포크가 진행됨에 따라 감소하는 경향이 있습니다. 이는 모
델이 학습 중에 성능을 향상시켰음을 나타냅니다.
 • 정확도(Accuracy) 또한 훈련 및 검증 데이터에서 상당히 높게 나타나지만, 정확도
만으로 모델의 성능을 평가하는 것은 편향된 결과를 가져올 수 있습니다. 특히 데
이터가 불균형할 때는 더 그렇습니다.
 • F1 점수와 ROC AUC Score는 정밀도, 재현율 및 클래스 간의 균형을 고려하여 더 
종합적인 성능 평가를 제공합니다.

tut1-model.pt 파일로부터 훈련된 모델의 상태 사전을 로드합니다. 이는 이전에 저장된 최적
의 모델 파라미터를 불러옵니다
test_loss, test_acc = evaluate(model, test_iterator, criterion)-evaluate함수를 사용하여 데이터셋에 대한 모델 평가를 수행-여기서 model은 훈련된 RNN모델-test_itertor은 테스트 데이터셋을 배치로 나눔-criterion은 손실함수로, 여기서는 nn,BCEwithLogistLoss()-손실과 정확도 뿐만 아니라 f1점수 및 ROC AUC점수도 반환
end_time = time.time()
 epoch_mins, epoch_secs = epoch_time(start_time, end_time)-에포크의 훈련 및 검증이 끝났으므로 소요시간을 계산
if valid_loss < best_valid_loss:
 best_valid_loss = valid_loss
 torch.save(model.state_dict(), 'tut1-model.pt')-현재까지의 최적 검증 손실보다 현재 검증 손실이 낮으면 best_valid_loss를 업데이트-현재 모델의 상태를 tut1-model.pt파일로 저장-최종적으로 검증 손실이 낮아진 최적의 모델을 저장
종합적으로, 테스트 결과는 다음과 같습니다:-테스트 손실(Test Loss): 0.438 //예측 오차의 평균-테스트 정확도(Test Accuracy): 85.78% //모델이 테스트 데이터에서 얼마나 정확-F1 Score: 0.103-ROC AUC Score: 0.582

#마지막
1.text필드를 사용하여 comment열을 처리(이전에 정의한 torchtext의 필드)
2.테스트 csv파일을 로드하고 comment열을 가져오고 이를 토대로 tabular데이터셋을 
생성
3.배치사이즈를 1로 설정하여 한번에 하나의 데이터 샘플을 처리하도록 함
4.사용가능한 경우에는 gpu를 사용하고 그렇지 않은 경우 cpu를 사용하도록 함
이후 구성된 이터레이터들을 사용하여 추론시에 데이터를 배치 단위로 처리할수 있도
록 함(각 배치에는 앞서 나왔듯 하나의 데이터 샘플만 포함)
최종적으로 테스트 csv의 파일을 악성댓글 탐지의 모델을 사용하여 
1.테스트 데이터의 악성 댓글 탐지를 예측하고
2.시그모이드 함수로 확률로 변환
3.확률을 라운드(반올림)하여 이진 예측을 생성
//0또는 1로 반올림하여 긍정인지 부정인지를 표현
4.예측 결과 및 확률을 리스트에 추가
5.데이터 프레임으로 정리하여 최종적으로 submission.csv파일에 저장

#아쉬운 점
기본적인 RNN을 사용하여 더 높은 정확도가 나오는데에 한계가 있었음
더 뛰어난 모델을 사용하거나 더 효과적인 알고리즘을 구축하였으면 더 만족스러운 
결과를 얻었겠지만 아직 지식의 부족으로 인해 최대한 RNN모델을 구축해서 학습을 
하고 예측을 하고 결과를 얻어내는 코드를 작성하였다
