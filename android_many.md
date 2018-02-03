# Android 많이 사용하는 것들

## Fragment
> 공식 문서 => https://developer.android.com/guide/components/fragments.html

- Activity 전환 없이 여러 화면(UI/Layout) 등을 구성하기 위해 사용
- 웹에서 iframe 역할을 한다고 생각하면 쉬움
- 때로는 액티비티 하나 더 띄우는게 나을수 있다. 모든걸 Fragment로 해결하려 하진 말자
- ~~라이프사이클이 이론대로 동작 안해서 골때림~~

### Fragment Class 구현 예

```java
import android.support.v4.app.Fragment;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;

public class MoreFragment extends Fragment {

    private static MoreFragment instance;
    public static MoreFragment getInstance(){
        if( instance==null ) instance = new MoreFragment();
        return instance;
    }
    public MoreFragment() {}

    public Context getContext(){ return (Context)getActivity(); }

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        final View rootview = inflater.inflate(R.layout.fragment_more, container, false);

        return rootview;
    }

}
```
- 앱생성시 낮은 버전까지 지원하면 support libray v4로...

### 레이아웃 xml

```xml
<fragment
    android:id="@+id/fragment"
    android:name="앱패키지.ContentsFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:layout="@layout/activity_main" />
```

### 전환 코드

```java
private void replaceFragment( Fragment fragment ){
  // fragment = MoreFragment.getInstance();
  FragmentTransaction ft = getSupportFragmentManager().beginTransaction();
  // support 라이브러리 v7이면 getFragmentManager()
  ft.replace( R.id.fragment, fragment ); // fragment로 사용할 id
  ft.commit();
}
```

## RecyclerView

### 이론이랄까...

- listview 처럼 제한된 화면에 대량의 데이터를 보여줄때 사용
- listview와 비교
  - 좀 더 다양하고 폭넓은 레이아웃 지원
  - 헤더, 푸터, 중간 레이아웃을 다르게 설정 가능
  - 모든 것을 직접 구현해야 함 (ex> Click Event)

- RecyclerView.LayoutManager : (ChildView의) 레이아웃의 배치를 전담
  - LinearLyoutManager : 리스트
  - GridLyoutManager : 그리드
  - StaggeredGridLyoutManager : 균등하지 않은 그리드
> orientation을 가짐..

- RecyclerView.Adapter : 데이터 모델과 연결
  - [필수구현] getItemCount : 전체 데이터 개수 전달
  - [필수구현] onCreateViewHolder : ViewHolder를 레이아웃 요소들을 세팅하여 전달
  - [필수구현] onBindViewHolder : VierHolder의 레이아웃에 데이터를 설정
  - onViewRecycled : 재활용 여부를 반환
  - notifyDataSetChanged : 데이터가 변경됬다
     - notifyItemChange, notifyItemInserted, notifyItemMoved, notifyItemRemoved 등도 지원

- RecyclerView.ViewHolder : 를 이용해 레이아웃에 데이터를 세팅
  - setIsRecyclable : 재활용 여부 설정
  - getAdapterPosition : 현재 view의 Adapter 상의 위치
  - getLayoutPosition : 보여지는 화면상의 위치
  - getItemViewType, getItemId

- RecyclerView.ItemAnimator
- RecyclerView.ItemDecoration

### 코드의 예

-  Adapter

```java
public class MyAdapter extends RecyclerView.Adapter<MyAdapter.ViewHolder> {

//    RecyclerView.OnItemTouchListener
    private String[] mDataset;
    // Provide a suitable constructor (depends on the kind of dataset)
    public MyAdapter(String[] myDataset) {
        mDataset = myDataset;
    }

    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        holder.tvTitle.setText(mDataset[position]);
    }

    @Override
    public int getItemCount() { return mDataset.length; }

    // Create new views (invoked by the layout manager)
    @Override
    public MyAdapter.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View v = LayoutInflater.from(parent.getContext()).inflate(R.layout.listitem_contents, parent, false);
        ViewHolder vh = new ViewHolder(v);
        return vh;
    }

    public static class ViewHolder extends RecyclerView.ViewHolder {
        public TextView tvTitle;
        public TextView tvDescriptions;
        public ViewHolder(View v) {
            super(v);
            tvTitle = v.findViewById(R.id.tv_text);
            tvDescriptions = v.findViewById(R.id.tv_description);
        }
    }
}
```

- 화면 class (Activity, Fragment)

```java

public void setRecyclerView(){
  mRecyclerView = (RecyclerView)findViewById(R.id.rv_contents);
  mRecyclerView.setHasFixedSize(true);
  // 레이아웃 매니저 설정
  mLayoutManager = new LinearLayoutManager(activity);
  mRecyclerView.setLayoutManager(mLayoutManager);
  // 데이터 설정
  String[] myDataset = {"소개", "호스팅", "인증", "실시간DB"};
  mAdapter = new MyAdapter(myDataset);
  mRecyclerView.setAdapter(mAdapter);
  // 데이터가 비동기로 가져올 경우 가져온 후에 notify
  // mAdapter.notifyDataSetChanged();
}
```

- 레이아웃 xml

```xml
<android.support.v7.widget.RecyclerView
    android:id="@+id/rv_contents"
    android:layout_weight="1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```
