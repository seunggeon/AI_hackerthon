# AI_hackerthon
AI_hackerthon

Error: 
1. dataframe 과 np.array 왔다갔다하기
pd.concat은 또 파라미터로 dataframe만 받는다. 
model(input)이나 transform이나 fit도 dataframe이었던 듯. 
dataframe은 iloc로 인덱스 접근해야되고, np.array는 일반 배열처럼 접근해야한다.
그리고 맨처음 해석할 때 reshape도 조심하기.

2. global 변수. 
칼만필터 알고리즘 main에 global변수가 있었는데 main을 kalman_excute로 함수화 하다보니 어떻게 해야될지 몰라서 그냥 해당 함수에 파라미터로 넣어버림.

4. def model과 scaler.
model input으로 shape=(scaler까지 완료된 데이터 형태[1],[2]) 가 들어가야함. 
완료된 데이터 형태 [1],[2]는 예를 들어 feature =8 이면 (train_split_id 갯수,600,8) 이렇게 되는데 (600,8)을 넣어야됨.
1등은 무슨 그냥 바로 위에 셀에서 전역변수 마냥 data_for_scaler 하고, def model에 넣어버리던데,
나는 그게 불확실하다 생각해서 model(input)를 추가함.
그랬더니 또 training_model(first_model, r_seed=2020, l_seed=2) 이 함수 실행하니까 파라미터를 못 받아오는 거 같아서, 그냥 없애고 순차적으로 실행되게끔 형태 바꿈.

4.scaler의 fit.
scaler의 fit부분에 train_dataset을 넣으려고 했는데 이 칼만필터가 보통 계산이 아닌듯.. 그래서 3125*600 하려니까 끝나질 않아.
그래서 그냥 일부분만 fit하고 말았는데 fit가 아마도 나중에는 제대로 돌려야할 듯.
fit은 우리 데이터의 각각 feature의 index =0 ~끝까지 평균과 분산을 구하는 함수.
transform은 정규분포에서 평균 0, 분산 1인 표준정규분포로 바꾸는 함수.
    scaler = StandardScaler()


    i = [1,3,4,5,6,7,8,9,100,1020,202]
    train_ACC, train_GY = train_reshape_acc[i].reshape(-1, 3), train_reshape_gy[i].reshape(-1, 3)
    data_for_scaler = train_dataset(pd.DataFrame(train_ACC), pd.DataFrame(train_GY))
    scale_X = scaler.fit_transform(data_for_scaler) 
 이런식으로 scaler에다가 계속 업데이트 시켜야됌.

5.칼만필터에서 쿼터니안에서 오일러로 바꾸는 거.
자꾸 math domain이라고,, 그 asin부분에서 -1이하 이거나 1 이상이면 나는 에러 발생함... 그래서 그냥 라이브러리 함수 갖다씀.
그 과정에서 !pip로 transform 설치완료.

6.**가장 큰 오류** 
train_split_id = [0,1,4,5,21] 이라 치고, train_ACC=train_acc_reshape[train_split_id].reshape() 하면 당연히 0부터 그냥 들어가나?
했는데 그게 아니라 배열이 5차원으로 생김. 즉, 배열안에 있는 모든 id 해당되게 배열 차원 늘리면서 생김.
그래서 kalman에서 나는 , 하나만 할당하는 줄 알고 for i in range(1000) train_ACC=train_acc_reshape[i].reshape() 이렇게 돌리고 확인하고 있었는데, 이게 아니라 
저 배열로 들어가기 때문에 NSAMPLE을 차원 수*600 만큼 해서, shape[0]으로 주었더니 정상적으로 작동.

7. model = first_model(input)을 for i in range(epochs) 안에 넣은 것. 
그렇게 되면 epoch 돌때마다 새로 선언이 되서 학습이 누적이 안된다.

8.아직 헷갈리는거: epoch마다 train_dataset을 바꿔야하나??
9.tf.random??

10.** 가장 큰 오류 변천사**
그 이전 에러 잡은 게 train_reshape_acc[id]로 부분 부분 들어올 줄 몰랐음.
그래서 이쪽 에러의 변천사를 살펴보면, train_reshape_acc로 칼만필터 input 올거라 예상.
train_reshape_acc[id]로 칼만필터 input 예상 대신 id는 배열이 아닌 그냥 하나의 값이라 예상 -> 그래서 NSAPLE은 1*600*8로 예상되어 600으로 고정.
찍어보니까 처음에만 값이 찍히고 뒤엔 다 NAN 떠서 계속 돌려도 아무것도 안바뀌고 train_accurancy =0.0037 validation loss= NAN valid_acc = 0.066 뭐 이따꾸로 
계속 동일하게 나옴...
알고보니, NAN이 너무 많아서 학습해도 의미없다 라는 뜻이었지..


