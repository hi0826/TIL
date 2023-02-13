# MVVM 패턴

- Model, View, View Model

## View Model

- View를 표현하기 위해 만들어진 View를 위한 Model
- Model과 유사하게 디자인됨
- Command 패턴과 Data Binding을 사용하면서 View와 ViewModel의 의존성 제거

### Data Binding

- viewModel의 속성과 특정 view의 속성을 binding시켜줌으로써 viewModel 속성이 변경 될때마다 view를 업데이트 시켜준다
