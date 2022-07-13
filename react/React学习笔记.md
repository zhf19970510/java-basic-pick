## React学习笔记

### 为什么学习React

声明式设计、高效灵活、组件化开发、单向数据流、虚拟dom

### 怎么学习React

费曼学习法

### 课程内容如何安排的

React 基础、React Hooks、React 路由、Redux、组件库、Immutable、Mobx、React+TS、单元测试、dva+umi

### React特点

1. 声明式设计-React采用声明范式，可以轻松描述应用。
2. 高效 - React通过对DOM的模拟（虚拟DOM），最大限度地减少与DOM的交互。
3. 灵活 - React可以与已知的库或框架很好地配合。
4. JSX - JSX 是JavaScript语法的扩展
5. 组件 - 通过React构建组件，使得代码更加容易得到复用，能够很好的应用在大项目的开发中。
6. 单向响应的数据流 - React实现了单向响应的数据流，从而减少了重复代码，这也是它为什么比传统数据绑定更简单。



### 虚拟DOM

![image-20220616222625377](D:\zhfGitRepository\java-basic-pick\react\React学习笔记.assets\image-20220616222625377.png)

![image-20220616223508826](D:\zhfGitRepository\java-basic-pick\react\React学习笔记.assets\image-20220616223508826.png)

使用create-react-app myapp 脚手架创建项目的时候需要等待一段时间，因为它会安装三个东西，react、react-dom、react-script

![image-20220617000222361](D:\zhfGitRepository\java-basic-pick\react\React学习笔记.assets\image-20220617000222361.png)

tip：npm源可以交给nrm进行管理：

![image-20220617221207919](D:\zhfGitRepository\java-basic-pick\react\React学习笔记.assets\image-20220617221207919.png)

所谓的JSX其实就是JavaScript对象，所以使用React和JSX的时候一定要经过编译的过程：

JSX-使用react构造组件，bable进行编译 —》JavaScript对象 —》Reactdom.render() —》DOM元素 —》插入页面

## 二.Class组件

### 五、组件的数据挂载方式

1. 状态（State)

状态就是组件描述某种显示情况的数据，由组件自己设置和修改，也就是说由组件自己维护，使用状态的目的就是为了在不同的状态下使组件的显示不同（自己管理）。

