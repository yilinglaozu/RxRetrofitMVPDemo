# rxjava2+retrofit2
## 1.mvp-V1
mvp乞丐版已知bug：请求异常会崩溃

## 2.mvp-V2
MVP-V2.0<br/>
增加MD5,工具类，Activity和Application基类<br/>
封装retrofit异常<br/>
修改回调接口<br/><br/>
#### 2018-12-30<br/>
修复rxjava观察者请求结果泛型报错问题

#### 2019-01-04<br/>
1.免测量的ScrollView:android.support.v4.widget.NestedScrollView<br/>
2.仿朋友圈的技术分析：<br/>
 - 测量GridView：参照ScrollView嵌套GridView<br/>
 - 如果有图片，则显示GridView，没图片则隐藏GONE<br/>
 - 如果有多张图片，按照一定的逻辑切换Griview的列数，在循环复用的时候切换gridview的adpter在创建的时候设置上，循环复用的时候，得到adpter清空数据，添加新数据刷新<br/>
3.页面布局关于长短页面的压盖问题<br/>

#### 2019-01-07<br/>
1.topdemo是顶部浮动效果（吸顶效果）测试<br/>
```
引入依赖：
implementation 'com.gavin.com.library:stickyDecoration:*'

使用：
PowerfulStickyDecoration decoration = PowerfulStickyDecoration.Builder
                .init(new PowerGroupListener() {
                    @Override
                    public String getGroupName(int position) {
                        //获取组名，用于判断是否是同一组
                        return myAdapter.getItem(position).getBrandId();
                    }

                    @Override
                    public View getGroupView(int position) {
                        //获取自定定义的组View
                            View view = getLayoutInflater()
                                    .inflate(R.layout.list_title, null, false);
                            ((TextView) view.findViewById(R.id.title))
                                    .setText(myAdapter.getItem(position).getBrand());
                            return view;
                    }
                })
                .setGroupHeight(getResources().getDimensionPixelSize(R.dimen.dip_40))//设置高度
                .build();
recyclerView.addItemDecoration(decoration);
```
<br/>

## 3.mvp-V3
TopDemo可视情况移除<br/>
重大升级：针对公司级大项目进行框架升级，V3版本停留于单工程形式，接下来的V4版本将组件化分层（请期待）
#### 2019-06-09<br/>
注：BasePresenter以下简称BP;NetworkManager为Retrofit网络工具类<br/>
1.修复rxjava无法停止请求的问题，BP中改为Dispose对象停止访问网络；<br/>
2.将IRquest接口每次请求都要新建的问题修复，放到Presenter构造方法中，提高对象使用率；<br/>
3.简化BP中异常处理类。。哈哈，抱歉，其实我学会使用Rxjava的onErrorReturn方法了，所以之前V2版本中BP异常处理就要被抛弃了<br/>
4.重点：对BP和DataCall进行重大升级：
 - ①BP可以根据业务选择不同的请求接口，例：
请求我们服务器用的IAppRequest，请求经纬度换算的百度接口，使用IBaiduRequest，
Presenter层继承BP，重写getRequestType()方法用于操作NetworkManager中不同域名的Retrofit；
 - ②BP根据结果不同，选择不同的Consumer进行处理，这里写法比较灵活，遵循基类不能经常修改的原则，
我暴露了getConsumer(Object...args)方法，你可以直接重写此方法修改Consumer用于操作返回值；
如果某个业务的Consumer被大量用到，你可以修改BP中getConsumer方法，然后重写getResponseType()即可。
例：我们项目接口有50个，由于和新浪微博深度合作资源共享，我们接入新浪微博的20个接口，项目用到百度定位，用了两个百度接口；
 - ③DataCall重写，由于某些业务需要通过请求值进行判断，这里我把请求值又回传给了页面层。<br/>
 
## 4.mvp-V4
TopDemo已经移除<br/>
重大升级：组件化MVP框架，应对多业务多小组大项目开发<br/>
1.ButterKnife使用，子module中的build.gradle必须包含：
```
apply plugin: 'com.jakewharton.butterknife'

annotationProcessor 'com.jakewharton:butterknife-compiler:10.1.0'
```

ButterKnife使用中的坑我已经帮各位踩过了，随便查看项目中的某一个Activity代码你都会发现，@BindView使用了R2（R2只适用于ButterKnife自己的类使用），
但是方法中所有资源使用R，这个坑很恶心的。<br/>
2.Arouter的引入和使用，网上博客千千万，最好参照github:https://github.com/alibaba/ARouter;<br/>
3.v4使用androidX，替换了appcompat.support，因为ButterKnife10.*之后使用了androidX。<br/>
4.暂时没有要普及的了，感谢自己。

