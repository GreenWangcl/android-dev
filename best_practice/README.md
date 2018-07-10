# 最佳实践

## 关于注释

- 类文件：功能描述，难点实现解释，参考来源
- 方法：重点方法介绍思路
- 变量：理解上容易造成歧义的变量必须加注释
- 代码注释：注释理解时比较晦涩的内容

## 使用 Sonar 进行代码静态检查

- 根据建议优化代码

## 弱引用

- 在使用弱引用时，不仅要判断是否为 null 还需要判断是否 isDetached，这两种情况下都需要重新创建引用对象

## 关于自定义组合View

- 使用 merge 减少嵌套
- 使用 tools:parentTag 属性在xml预览时使用期望的父类型
- xml 中 merge 下的根属性在 View 实际创建时是不生效的，需要在 View 中重新设置
- 避免使用 LayoutInflater.from(context).inflate(R.layout.xxx, null)，尽量使用 LayoutInflater.from(context).inflate(R.layout.xxx, this);

## 关于 Activity、Fragment 传参

- 为了封装 Activity、Fragment 参数传递过程，Activity 提供封装后的 public static void start(xxx) 方法， Fragment 提供封装后的 public static xxFragment newInstance(xxx) 方法
- 并且相应的的 public static 方法应该尽量放在类最开始的地方，让调用者在打开类文件后更快的看到如何调用
- 传参时使用 Parcelable 效率高于 Serializable，使用 AS 插件可以快速将类装换到 Parcelable

## EventBus

- 统一定义 Event 数据类型，并对不同类型的 ID 进行统一管理
- 必须显示指定监听所在线程，避免让维护者如果所有代码才能了解所在线程

## RecyclerView

- 在进行数据 bind 时，需要 ViewHolder 提供类似 bind、bindData 方法进行数据绑定，避免在 ViewBinder 里进行绑定
- 建议使用第三方库 MultiType 优化对 RecyclerView 的使用
- 避免使用 LayoutInflater.from(context).inflate(R.layout.xxx, null)，尽量使用 LayoutInflater.from(context).inflate(R.layout.xxx, parent, false);
- 通知列表更新尽量避免使用 notifyDataSetChanged 方法全部刷新，尽量使用 notifyItemChanged(int position) 和 notifyItemChanged(int position, Object payload) 方法减少刷新范围

## SharePrefrence 的使用

- 使用封装后的 helper 类
- key 值不对外开放，提供业务需要的接口隐藏存取过程
