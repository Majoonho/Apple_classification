# Apple_classification

pytorch dataload parameters
dataset
Dataset
torch.utils.data.Dataset의 객체를 사용해야 합니다.

참고로 torch의 dataset은 2가지 스타일이 있습니다.

Map-style dataset
index가 존재하여 data[index]로 데이터를 참조할 수 있음
__getitem__과 __len__ 선언 필요
Iterable-style dataset
random으로 읽기에 어렵거나, data에 따라 batch size가 달라지는 데이터(dynamic batch size)에 적합
비교하자면 stream data, real-time log 등에 적합
__iter__ 선언 필요

shuffle
bool, optional, default=False
데이터를 DataLoader에서 섞어서 사용하겠는지를 설정할 수 있습니다. 실험 재현을 위해 torch.manual_seed를 고정하는 것도 포인트입니다.

num_workers
int, optional, default=0
데이터 로딩에 사용하는 subprocess개수입니다. (멀티프로세싱)

기본값이 0인데 이는 data가 main process로 불러오는 것을 의미합니다. 그럼 많이 사용하면 좋지 않은가? 라고 질문하실 수도 있습니다.

하지만 데이터를 불러 CPU와 GPU 사이에서 많은 교류가 일어나면 오히려 병목이 생길 수 있습니다. 이것도 trade-off관계인데, 이와 관련하여는 다음 글을 추천합니다.

pin_memory
bool, optional
True러 선언하면, 데이터로더는 Tensor를 CUDA 고정 메모리에 올립니다.

어떤 상황에서 더 빨라질지는 다음 글을 참고합시다.

discuss.Pytorch : When to set pin_memory to true?

drop_last
bool, optional
batch 단위로 데이터를 불러온다면, batch_size에 따라 마지막 batch의 길이가 달라질 수 있습니다. 예를 들어 data의 개수는 27개인데, batch_size가 5라면 마지막 batch의 크기는 2가 되겠죠.

batch의 길이가 다른 경우에 따라 loss를 구하기 귀찮은 경우가 생기고, batch의 크기에 따른 의존도 높은 함수를 사용할 때 걱정이 되는 경우 마지막 batch를 사용하지 않을 수 있습니다.
