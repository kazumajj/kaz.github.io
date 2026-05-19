---
title: "native的0033"
---

### 创建修改样式：

```typescript
StyleSheet.create(
  {
    container:{
      ..//正常写一些样式
    }
  })
  //是一个js对象而非css
  //元素默认自带display:flex
  //样式的优先级是看数组中的顺序
```

### 为什么不用传统的css

1.传统 CSS 是为 Web 浏览器的 DOM 渲染设计的,而手机的员工生空间是无法解析的，通过用`StyleSheet`把样式变为js对象，这样可以当作对象里的属性用。

2.在ios和安卓可以自动适配，并用`Platform.select`单独改一些样式，

## react native的一些组件

**FlatList长列表组件**数据在data ,渲染长列表（自身就已经形成滚动无需外层用ScrollView）,也可以结合RefreshControl实现下拉刷新

**SectionList带有分组标签的长列表**数据在sections

**ScrollView组件**结合RefreshControl实现下拉刷新

与map差异：map直接一次性渲染全部会耗费性能

```javascript
const App = () => {
  const [refresh,setRefreshing]=useState(false)
  const onRefresh=()=>{
    setRefreshing(true)
    console.log('开始读取接口！');
    setTimeout(() => {
      setRefreshing(false)
    }, 1000);//计时器处替换成请求
  }
  
<ScrollView style={[styles.scrollView,styles.container]}
refreshControl={<RefreshControl refreshing={refresh} 
onRefresh={onRefresh}> </RefreshControl>}>
<Scrollview>
```

### Alert 弹窗（跨平台差异）

```tsx
import { Alert, Button } from 'react-native';

const AlertDemo = () => (
  <Button
    title="弹出提示"
    onPress={() => {
      Alert.alert(
        '标题',          // 标题（必传）
        '安卓最多显示3个按钮，iOS无限制', // 具体内容
        [
          { text: '取消', style: 'cancel' }, // 取消按钮（style仅iOS生效）
          { text: '确认', onPress: () => console.log('确认') }
        ],
        { cancelable: false } // 是否可点击空白处关闭
      );
    }}
  />
);
```

- **Android**：最多显示3个按钮，按钮横向排列；
- **iOS**：无按钮数量限制，按钮纵向排列

### 路由router

|方法|作用|说明|
|--|--|--|
|router.navigate|跳转到指定页面，最常用。|如果目标页面已在 Stack 中，直接跳转到现有实例，否则新增加页面到 Stack。|
|router.replace|替换掉 Stack 中所有页面。|因为 Stack 里面的页面都被替换了，无法返回上一页。|
|router.push|强制新增加页面到 Stack。|无论目标页面是否存在，始终在 Stack 里新增一个页面。|
|router.back|返回上一个页面。|根据目标页面是否存在，跳转回上一页。|
|router.dismiss|关闭模态页（Modal/no-found）|关闭弹窗式页面|

### 区分不同平台

**1.Patfrom**

使用Platform.OS在 iOS 上会返回字符串ios，而在 Android 设备或模拟器上则会返回android，Platform.select()以 Platform.OS 为 key，从传入的对象中返回对应平台的值

**2.文件后缀**

比如Button.ios.js，Button.android.js，Button.native.js(web端好像不建议)这些文件后缀名会被react native自动检测

### expo router与react router

|特性|**Expo Router**|**React Router**|
|--|--|--|
|**平台**|专为 **React Native (Expo)** 设计|专为 **Web** 设计，支持 React Native 需要额外配置|
|**路由配置**|基于文件系统自动配置（看文件名）|需要显式声明路由|
|**用法简便性**|更简洁，自动化配置，适用于快速开发|需要手动配置，灵活性更高|
|**功能扩展**|主要针对移动端和跨平台开发  |功能全面，适用于 Web 和跨平台开发|
|**集成度**|与 Expo 紧密集成|独立库，适用于各种 React 项目|

nativewind配置问题

### View与Animated.View

|特性|View|Animated.View|
|--|--|--|
|静态样式|支持（`style` 属性）|支持（和 View 一致）|
|动画属性|不支持|支持（如 `opacity`、`transform` 可绑定动画值）|
|性能|普通渲染|基于原生驱动（`useNativeDriver`），动画更流畅|
|依赖|React Native 内置，无需额外安装|依赖 `react-native-reanimated` 或 React Native 内置 `Animated`|

### useSafeAreaInsets()安全区域使用

根组件用 SafeAreaProvider 包裹（必须！）；
页面里调用 insets= useSafeAreaInsets() 拿到；
用 insets.top/insets.bottom 调整布局的 padding/margin，避开灵动岛这种遮挡

（1）全局配置（根组件）

```tsx
export default function RootLayout() {
  return (
    <SafeAreaProvider> {/* 必须包裹根组件 */}
      <Stack>
        {/* 路由内容 */}
      </Stack>
    </SafeAreaProvider>
  );
}
```

#### （2）页面中使用

```tsx
const SafeAreaDemo = () => {
  const insets = useSafeAreaInsets(); // 获取安全区域值
  // insets.top，bottom，left,right
  return (
    <View
      style={{
        flex: 1,
        // 避开顶部安全区域
        paddingTop: insets.top,

        paddingBottom: insets.bottom,
        backgroundColor: '#f5f5f5'
      }}
    >
      {/* 具体内容 */}
    </View>
  );
};
```

## 打包上架（Expo 项目）？？？还没学到。
