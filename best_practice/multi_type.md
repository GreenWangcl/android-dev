# 推荐使用开源库 [MultiType](https://github.com/drakeet/MultiType/) 简化 RecyclerView 在多样式情况下的开发

目前项目中使用开源库 [BaseRecyclerViewAdapterHelper](https://github.com/CymChad/BaseRecyclerViewAdapterHelper) 简化了 RecyclerView 在开发过程中的代码量，但是在处理多样式的 RecyclerView 时发现 Adapter 的复杂度还是比较高。而 [MultiType](https://github.com/drakeet/MultiType/) 只侧重于解决多样式 RecyclerView 的问题，具有更强的解耦能力。

## 可用性分析

### 引入方式

可以直接通过 Gradle 引入

```
dependencies {
    implementation 'me.drakeet.multitype:multitype:3.4.4' // 如果已经使用 androidx，可以使用最新 3.5.0 版本
}
```

### 库大小（基于 MultiType v3.4.4）

- multitype-3.4.4-sources.jar: 19k
- multitype-3.4.4.aar: 16k

引入后不会明显增加安装包大小。

### License

MultiType [声明](https://github.com/drakeet/MultiType/#license) 使用 `Apache 2.0` 的 License，引入后项目不会产生 License 相关问题。

## 在使用多样式 RecyclerView 时 MultiType 与 BaseRecyclerViewAdapterHelper 的比较

| 纬度        | MultiType                                                                                                                                              | BaseRecyclerViewAdapterHelper                                                                                            |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------ |
| Adapter      | 直接 new MultiTypeAdapter()，不再需要写 Adapter 相关代码                                                                                               | 实现一个继承自BaseMultiItemQuickAdapter的 Adapter                                                                        |
| 数据源实体类 | 可以是任意实体类                                                                                                                                       | 实体类必须实现 MultiItemEntity                                                                                           |
| ViewHolder   | 可以根据实体类或自定义类型值绑定不同的 ItemViewBinder ，而 ItemViewBinder 可以使用不同的 ViewHolder ，有利于 ViewHolder 解耦，具体请看后面的示例代码 | 在 Adapter 中需要指定对应的 ViewHolder ，如果需要结构需要在 Adapter 中判断使用 ViewHolder ，因此会增加 Adapter 的复杂性 |

## MultiType 实例代码

### Step 1. 创建数据实体类
```
public class TextItem {

    public final @NonNull String text;

    public TextItem(@NonNull String text) {
        this.text = text;
    }
}
```

### Step 2. 创建继承 ItemViewBinder<T, VH extends ViewHolder> 用于数据绑定和实现对应的 ViewHolder
```
public class TextItemViewBinder extends ItemViewBinder<TextItem, TextItemViewBinder.TextHolder> {
    static class TextHolder extends RecyclerView.ViewHolder {
        private @NonNull final TextView text;

        TextHolder(@NonNull View itemView) {
            super(itemView);
            this.text = (TextView) itemView.findViewById(R.id.text);
        }
    }

    @NonNull @Override
    protected TextHolder onCreateViewHolder(@NonNull LayoutInflater inflater, @NonNull ViewGroup parent) {
        View root = inflater.inflate(R.layout.item_text, parent, false);
        return new TextHolder(root);
    }

    @Override
    protected void onBindViewHolder(@NonNull TextHolder holder, @NonNull TextItem textItem) {
        holder.text.setText("hello: " + textItem.text);
        Log.d("demo", "position: " + getPosition(holder));
        Log.d("demo", "adapter: " + getAdapter());
    }
}
```

### Step 3. 使用 MultiTypeAdapter 注册多类型

```
// 类型注册
adapter = new MultiTypeAdapter();
adapter.register(TextItem.class, new TextItemViewBinder());
adapter.register(ImageItem.class, new ImageItemViewBinder());
adapter.register(RichItem.class, new RichItemViewBinder());
adapter.register(Data.class).to(
    new DataType1ViewBinder(),
    new DataType2ViewBinder()
).withClassLinker((position, data) -> {
    if (data.type == Data.TYPE_2) {
        return DataType2ViewBinder.class;
    } else {
        return DataType1ViewBinder.class;
    }
});
recyclerView.setAdapter(adapter);

// 数据展示
items = new Items();
items.add(textItem);
items.add(imageItem);
items.add(richItem);
items.add(data1)
items.add(data2)
adapter.setItems(items);
adapter.notifyDataSetChanged();
```
