# 相关讨论
[原链接](https://stackoverflow.com/questions/49568073/react-context-vs-react-redux-when-should-i-use-each-one)

由于 **Context** 不再是一个实验性功能，您可以直接在应用程序中使用 Context，它将非常适合将数据传递到其设计目的的深层嵌套组件。

正如马克·埃里克森 (Mark Erikson) 在他的[博客](http://blog.isquaredsoftware.com/2018/03/redux-not-dead-yet/) 中所写：

> 如果您只是使用 Redux 来避免传递 props，那么 context 可以取代 Redux - 但您可能一开始就不需要 Redux。
>
> Context 也不提供像“Redux DevTools”之类的功能、跟踪状态更新的能力、添加集中式应用程序逻辑的“中间件”以及“Redux”启用的其他强大功能。

“Redux”功能更强大，提供了“Context API”所不提供的大量功能，正如 **@danAbramov** 提到的

> React Redux 在内部使用上下文，但它不会在公共 API 中公开这一事实。因此，您应该感觉通过 React Redux 使用上下文比直接使用上下文更安全，因为如果它发生变化，更新代码的负担将由 React Redux 承担，而不是您。

由 Redux 实际更新其实现以符合最新的 Context API。

最新的 Context API 可用于仅使用 Redux 在组件之间传递数据的应用程序，但是使用集中式数据并使用“redux-thunk”或“redux-saga”在 Action 创建者中处理 API 请求的应用程序仍然需要终极版。除此之外，Redux 还有其他与之关联的库，例如“redux-persist”，它允许您在 localStorage 中保存/存储数据并在刷新时重新水化，这是 Context API 仍然不支持的。

正如 @dan_abramov 在他的博客中提到的[你可能不需要 Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)，Redux 有一些有用的应用程序，例如

> * 将状态保留到本地存储，然后从中启动，开箱即用。
> * 在服务器上预填充状态，以 HTML 形式将其发送到客户端，然后从中启动，开箱即用。
> * 序列化用户操作并将其与状态快照一起附加到自动错误报告中，以便产品开发人员\
> 可以重播它们以重现错误。
> * 通过网络传递操作对象以实现协作环境，而无需对代码的编写方式进行重大更改。
> * 维护撤消历史记录或实现乐观突变，而无需对代码的编写方式进行重大更改。
> * 在开发中的状态历史记录之间移动，并在代码更改时从操作历史记录中重新评估当前状态，ala TDD。
> * 为开发工具提供全面的检查和控制功能，以便产品开发人员可以为其应用程序构建自定义工具。
> * 提供替代 UI，同时重用大部分业务逻辑。

对于如此多的应用程序，现在说 Redux 将被新的 Context API 取代还为时过早。


**如果您使用 Redux 只是为了避免将 props 传递给深层嵌套组件**，那么您可以将 Redux 替换为“Context” API。它正是针对此用例而设计的。

另一方面，**如果您将 Redux 用于其他所有事情**（拥有可预测的状态容器、在组件之外处理应用程序的逻辑、集中应用程序的状态、使用 [Redux DevTools](https://github.com/ReduxDevTools)。 com/zalmoxisus/redux-devtools-extension）来跟踪应用程序状态发生变化的时间、地点、原因和方式，或使用 [Redux Form](https://github.com/erikras/redux-form/) 等插件、[Redux Saga](https://github.com/redux-saga/redux-saga)、[Redux 撤消](https://github.com/omnidan/redux-undo)、[Redux 持久](https: //github.com/rt2zz/redux-persist) , [Redux Logger](https://github.com/evgenyrodionov/redux-logger), 等等…)，那么你绝对没有理由放弃 Redux。 `Context` API 不提供任何这些。

我个人认为，**Redux DevTools 扩展**是一个令人惊叹的、被低估的调试工具，它本身就证明了继续使用 Redux 的合理性。

一些参考：

* [Redux 还没有死！](http://blog.isquaredsoftware.com/2018/03/redux-not-dead-yet)
* [你可能不需要 Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)
* [React Hooks 会取代 Redux 吗？](https://medium.com/javascript-scene/do-react-hooks-replace-redux-210bab340672)
