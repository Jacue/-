// 添加约束
``` swift
+(instancetype)constraintWithItem:(id)view1 attribute:(NSLayoutAttribute)attr1 relatedBy:(NSLayoutRelation)relation toItem:(nullable id)view2 attribute:(NSLayoutAttribute)attr2 multiplier:(CGFloat)multiplier constant:(CGFloat)c;
```

>view1：设置约束的对象（视图）
>attr1：设置相应的约束属性
>relation：参照关系（=，>=，<=）
>view2：参照物（视图）
>attr2：参照物的相应约束属性
>multiplier：倍数
>c：偏移量

从Masonry的使用中，我们看看库中各个类的分工：
``` swift
[blackView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.left.top.mas_equalTo(self.view).offset(80);
        make.size.mas_equalTo(CGSizeMake(50, 50));
}];
``` 
mas_makeConstraints中生成MASConstraintMaker对象，然后由开发人员在block中设置相应约束，再通过maker的install方法以constraintWithItem的方式添加约束。

MASConstraintMaker对外暴露各种可设置的约束属性（left、right、size等），内部持有view（对应上面的view1）和constraints（添加到view上的约束）

block中是通过链式语法来设置相应约束的，值得一提的是，第一个点语法返回的是MASViewConstraint，第二个点语法返回的是MASCompositeConstraint（持有元素为MASViewConstraint的数组childConstraints），二者都继承于MASConstraint。每次点语法都会更新MASConstraintMaker的constraints属性，constraints的元素个数为block中的链式语法数量。

MASConstraint对外暴露各种可设置的约束属性（left、right、size等）以及与参照物的比对方法mas_equalTo、mas_offset等

MASViewConstraint继承于MASConstraint，包含单个约束的具体信息。其中的各属性分别对应系统api中参数如下：
##Public
>firstViewAttribute （MASViewAttribute） -> attr1
>secondViewAttribute （MASViewAttribute）-> attr2
##Private
>installedView -> view1和view2的父视图
>layoutRelation -> relation
>layoutConstant -> c
>layoutMultiplier -> multiplier
>layoutPriority 优先级
>layoutConstraint 由以上属性生成的约束

其中，MASViewAttribute持有单个约束的信息，view（如果不是视图，为空），item（视图），layoutAttribute（单个约束）

链式语法中，mas_equalTo之前为设置firstViewAttribute，mas_equalTo之后设置secondViewAttribute。


