# MultiStyle
一个支持多种item类型的recycleView依赖注入库

1.通过注解的方式方便的把ViewHolder注入到recycleView中。

2.自动view状态保存，不用关心因为复用导致的view显示不正确。

3.去除编写adapter那些冗余逻辑，只需要编写proxy和holder即可快速的实现淘宝首页那样复杂的页面

how to use

    maven {
        url "http://45.62.110.28:8081/nexus/content/repositories/multistyle/"
    }

    annotationProcessor 'com.syiyi:multistyle-compiler:1.3.3'
    compile 'com.syiyi:multistyle-annotations:1.3.3'
    compile 'com.syiyi:multistyle:1.3.3'
    
```
public class Activi extends Activity{
    static List<Content> DATA = new ArrayList<>();
    static final int TYPE_TEXT = H.id.TextHolder_com_syiyi_multistyle_holder;
    static final int TYPE_IMAGE = H.id.ImageHolder_com_syiyi_multistyle_holder;

    static {
        DATA.add(new Content(TYPE_TEXT, "hello"));
        DATA.add(new Content(TYPE_TEXT, "I"));
        DATA.add(new Content(TYPE_TEXT, "am"));
        DATA.add(new Content(TYPE_IMAGE, "http://e.hiphotos.baidu.com/image/pic/item/f7246b600c338744e7162094550fd9f9d62aa002.jpg"));
        DATA.add(new Content(TYPE_TEXT, "am"));
        DATA.add(new Content(TYPE_IMAGE, "http://e.hiphotos.baidu.com/image/pic/item/8cb1cb1349540923592e4e479758d109b3de4947.jpg"));
    }

    private MultiStyleAdapter<Content> adapter;
    private DiffHelper<Content> mDiffHelper;
    private Random random;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ViewHelper mHelper = new ViewHelper(this.getWindow().getDecorView().findViewById(android.R.id.content), this);
        RecyclerView list = mHelper.getView(R.id.list);
        list.setLayoutManager(new LinearLayoutManager(this, LinearLayoutManager.VERTICAL, false));
        list.addItemDecoration(new DividerItemDecoration(this, DividerItemDecoration.VERTICAL));
        adapter = new MultiStyleAdapter<Content>() {};
        mDiffHelper=new DiffHelper<>(adapter);
        MultiStyleAdapter.setDebug(true);
        list.setAdapter(adapter);
        mDiffHelper.setList(DATA);

    }

    public void onClick(View view) {
        if (random == null) {
            random = new Random(1000);
        }
        Content c1 = new Content(TYPE_TEXT, "今天天气真好" + random.nextInt(1000));

        Content c2 = new Content(TYPE_IMAGE, "http://b.hiphotos.baidu.com/image/pic/item/7acb0a46f21fbe09022d2ecb6f600c338644adfa.jpg");
        c2.text = "我是2";

        Content c3 = new Content(TYPE_TEXT, "今天好" + random.nextInt(1000));

        List<Content> temp = new ArrayList<>();
//        temp.add(c1);
//        temp.add(c2);
        for (int i=0;i<20;i++){
            Content a = new Content(TYPE_TEXT, "今天天气真好"+random.nextInt(1000) + random.nextInt(1000));
            temp.add(a);
        }
        //insert
        mDiffHelper.insertList(6,temp);

//        //remove
//        mDiffHelper.removeList(0, 1);
//        mDiffHelper.removeList(0, 2);
//        mDiffHelper.removeList(1, 2);
//        mDiffHelper.removeFirst();
//        mDiffHelper.removeLast();
//
        //update
//        List<Content> newList = new ArrayList<>();
//        newList.add(c1);
//        newList.add(c3);

//        //批量更新
//        List<Content> oldList = new ArrayList<>();
//        Content d1 = adapter.getItem(0);
//        c1.id = d1.id;
//        Content d2 = adapter.getItem(1);
//        c3.id = d2.id;
//
//        oldList.add(d1);
//        oldList.add(d2);

//        mDiffHelper.updateList(oldList, newList);
    }

    public static class Content implements MultiViewModel {

        public String id;

        Content(int viewType, String content) {
            this.viewType = viewType;
            this.content = content;
            id = UUID.randomUUID().toString();
        }

        private int viewType;
        private String content;
        public String text = "default";


        public String getContent() {
            return content;
        }


        @Override
        public int getViewTypeId() {
            return viewType;
        }

        @Override
        public String getViewTypeName() {
            return null;
        }

        @Override
        public boolean areContentsTheSame(MultiViewModel newModel) {
            if (!(newModel instanceof Content))
                return false;
            Content temp = (Content) newModel;
            return content.equals(temp.getContent());
        }

        @Override
        public Object getChangePayload(MultiViewModel newModel) {
            Content temp = (Content) newModel;
            return temp.getContent();
        }

        @Override
        public boolean areItemsTheSame(MultiViewModel newModel) {
            if (!(newModel instanceof Content))
                return false;
            Content temp = (Content) newModel;
            return id.equals(temp.id);
        }

        @Override
        public void resetPlayLoadData(MultiViewModel newModel) {
            Content temp = (Content) newModel;
            content = temp.getContent();
        }

        @Override
        public int hashCode() {
            return id.hashCode();
        }
    }
    ```
