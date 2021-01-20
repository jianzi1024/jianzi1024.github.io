---
layout: post
title: 学习笔记 - Google官方文档 - 核心主题 - Activity
author: 贱子
date: 2021-01-20
tags: [Android]
---

学习笔记 - Google官方文档 - 核心主题 - Activity <!--more-->

## Activity

### 生命周期

1. 为了在 Activity 生命周期的各个阶段之间导航转换，Activity 类提供六个核心回调：`onCreate()`、`onStart()`、`onResume()`、`onPause()`、`onStop()` 和 `onDestroy()`。当 Activity 进入新状态时，系统会调用其中每个回调。

   ![Android声明后期](/images/posts/android/activity_lifecycle.png)

2. `onCreate()`

   您必须实现此回调，它会在系统首次创建 Activity 时触发。Activity 会在创建后进入“已创建”状态。在 onCreate() 方法中，您需执行基本应用启动逻辑，该逻辑在 Activity 的整个生命周期中只应发生一次。例如，`onCreate()` 的实现可能会将数据绑定到列表，将 Activity 与 `ViewModel` 相关联，并实例化某些类作用域变量。此方法会接收 `savedInstanceState` 参数，后者是包含 Activity 先前保存状态的 `Bundle` 对象。如果 Activity 此前未曾存在，`Bundle` 对象的值为 null。

3. `onStart()`

   当 Activity 进入“已开始”状态时，系统会调用此回调。`onStart()` 调用使 Activity 对用户可见，因为应用会为 Activity 进入前台并支持互动做准备。例如，应用通过此方法来初始化维护界面的代码。

   `onStart()` 方法会非常快速地完成，并且与“已创建”状态一样，Activity 不会一直处于“已开始”状态。一旦此回调结束，Activity 便会进入“已恢复”状态，系统将调用 `onResume()` 方法。

4. `onResume()`

   Activity 会在进入“已恢复”状态时来到前台，然后系统调用 `onResume()` 回调。这是应用与用户互动的状态。应用会一直保持这种状态，直到某些事件发生，让焦点远离应用。此类事件包括接到来电、用户导航到另一个 Activity，或设备屏幕关闭。

   当发生中断事件时，Activity 进入“已暂停”状态，系统调用 `onPause()` 回调。

   如果 Activity 从“已暂停”状态返回“已恢复”状态，系统将再次调用 onResume() 方法。因此，您应实现 onResume()，以初始化在 `onPause()` 期间释放的组件，并执行每次 Activity 进入“已恢复”状态时必须完成的任何其他初始化操作。

5. `onPause()`

   系统将此方法视为用户将要离开您的 Activity 的第一个标志（尽管这并不总是意味着 Activity 会被销毁）；此方法表示 Activity 不再位于前台（尽管在用户处于多窗口模式时 Activity 仍然可见）。使用 `onPause()` 方法暂停或调整当 Activity 处于“已暂停”状态时不应继续（或应有节制地继续）的操作，以及您希望很快恢复的操作。Activity 进入此状态的原因有很多。例如：

   - 如 `onResume()` 部分所述，某个事件会中断应用执行。这是最常见的情况。
   - 在 Android 7.0（API 级别 24）或更高版本中，有多个应用在多窗口模式下运行。无论何时，都只有一个应用（窗口）可以拥有焦点，因此系统会暂停所有其他应用。
   - 有新的半透明 Activity（例如对话框）处于开启状态。只要 Activity 仍然部分可见但并未处于焦点之中，它便会一直暂停。

   您还可以使用 `onPause()` 方法释放系统资源、传感器（例如 GPS）手柄，或当您的 Activity 暂停且用户不需要它们时仍然可能影响电池续航时间的任何资源。然而，正如上文的 onResume() 部分所述，如果处于多窗口模式，“已暂停”的 Activity 仍完全可见。因此，您应该考虑使用 onStop() 而非 onPause() 来完全释放或调整与界面相关的资源和操作，以便更好地支持多窗口模式。

   `onPause()` 执行非常简单，而且不一定要有足够的时间来执行保存操作。因此，您**不**应使用 `onPause()` 来保存应用或用户数据、进行网络调用或执行数据库事务。因为在该方法完成之前，此类工作可能无法完成。相反，您应在 `onStop()` 期间执行高负载的关闭操作。

   `onPause()` 方法的完成并不意味着 Activity 离开“已暂停”状态。相反，Activity 会保持此状态，直到其恢复或变成对用户完全不可见。如果 Activity 恢复，系统将再次调用 `onResume()` 回调。如果 Activity 从“已暂停”状态返回“已恢复”状态，系统会让 `Activity` 实例继续驻留在内存中，并会在系统调用 `onResume()` 时重新调用该实例。在这种情况下，您无需重新初始化在任何回调方法导致 Activity 进入“已恢复”状态期间创建的组件。如果 Activity 变为完全不可见，系统会调用 `onStop()`。

6. `onStop()`

   如果您的 Activity 不再对用户可见，说明其已进入“已停止”状态，因此系统将调用 `onStop()` 回调。例如，当新启动的 Activity 覆盖整个屏幕时，可能会发生这种情况。如果 Activity 已结束运行并即将终止，系统还可以调用 `onStop()`。

   在 `onStop()` 方法中，应用应释放或调整在应用对用户不可见时的无用资源。例如，应用可以暂停动画效果，或从精确位置更新切换到粗略位置更新。使用 `onStop()` 而非 `onPause()` 可确保与界面相关的工作继续进行，即使用户在多窗口模式下查看您的 Activity 也能如此。

   您还应使用 `onStop()` 执行 CPU 相对密集的关闭操作。例如，如果您无法找到更合适的时机来将信息保存到数据库，可以在 `onStop()` 期间执行此操作。

   当您的 Activity 进入“已停止”状态时，`Activity` 对象会继续驻留在内存中：该对象将维护所有状态和成员信息，但不会附加到窗口管理器。Activity 恢复后，Activity 会重新调用这些信息。您无需重新初始化在任何回调方法导致 Activity 进入“已恢复”状态期间创建的组件。系统还会追踪布局中每个 `View` 对象的当前状态，如果用户在 `EditText` 微件中输入文本，系统将保留文本内容，因此您无需保存和恢复文本。

   **注意**：Activity 停止后，如果系统需要恢复内存，可能会销毁包含该 Activity 的进程。即使系统在 Activity 停止后销毁相应进程，系统仍会保留 `Bundle`（键值对的 blob）中 `View` 对象（例如 `EditText` 微件中的文本）的状态，并在用户返回 Activity 时恢复这些对象。

   进入“已停止”状态后，Activity 要么返回与用户互动，要么结束运行并消失。如果 Activity 返回，系统将调用 `onRestart()`。如果 `Activity` 结束运行，系统将调用 `onDestroy()`。

7. `onDestroy()`

   销毁 Ativity 之前，系统会先调用 `onDestroy()`。系统调用此回调的原因如下：

   1. Activity 即将结束（由于用户彻底关闭 Activity 或由于系统为 Activity 调用 `finish()`），或者
   2. 由于配置变更（例如设备旋转或多窗口模式），系统暂时销毁 Activity

   您应使用 `ViewModel` 对象来包含 Activity 的相关视图数据，而不是在您的 Activity 中加入逻辑来确定 Activity 被销毁的原因。如果因配置变更而重新创建 Activity，ViewModel 不必执行任何操作，因为系统将保留 ViewModel 并将其提供给下一个 Activity 实例。如果不重新创建 Activity，ViewModel 将调用 `onCleared()` 方法，以便在 Activity 被销毁前清除所需的任何数据。

   您可以使用 `isFinishing()` 方法区分这两种情况。

   如果 Activity 即将结束，onDestroy() 是 Activity 收到的最后一个生命周期回调。如果由于配置变更而调用 onDestroy()，系统会立即新建 Activity 实例，然后在新配置中为新实例调用 `onCreate()`。

   `onDestroy()` 回调应释放先前的回调（例如 `onStop()`）尚未释放的所有资源。

### 状态变更

1. 如果 Activity 位于前台，并且用户点按了**返回**按钮，Activity 将依次经历 `onPause()`、`onStop()` 和 `onDestroy()` 回调。活动不仅会被销毁，还会从返回堆栈中移除。

   需要注意的是，在这种情况下，默认不会触发 `onSaveInstanceState()` 回调。此行为基于的假设是，用户点按**返回**按钮时不期望返回 Activity 的同一实例。不过，您可以通过替换 `onBackPressed()` 方法实现某种自定义行为，例如“confirm-quit”对话框。

   如果您替换 `onBackPressed()` 方法，我们仍然强烈建议您从被替换的方法调用 `super.onBackPressed()`。否则，**返回**按钮的行为可能会让用户感觉突兀。

### 任务和返回栈

1. 任务是用户在执行某项工作时与之互动的一系列 Activity 的集合。这些 Activity 按照每个 Activity 打开的顺序排列在一个返回堆栈中。例如，电子邮件应用可能有一个 Activity 来显示新邮件列表。当用户选择一封邮件时，系统会打开一个新的 Activity 来显示该邮件。这个新的 Activity 会添加到返回堆栈中。如果用户按**返回**按钮，这个新的 Activity 即会完成并从堆栈中退出。

2. Android 7.0（API 级别 24）及更高版本支持多窗口环境，当应用在这种环境中同时运行时，系统会单独管理每个窗口的任务；而每个窗口可能包含多项任务。在 Chromebook 上运行的 Android 应用也是如此：系统按窗口管理任务或任务组。

3. 任务栈

   ![任务栈](/images/posts/android/diagram_backstack.png)

4. 如果用户继续按**返回**，则堆栈中的 Activity 会逐个退出，以显示前一个 Activity，直到用户返回到主屏幕（或任务开始时运行的 Activity）。移除堆栈中的所有 Activity 后，该任务将不复存在。

5. 多个任务可以同时在后台进行。但是，如果用户同时运行很多后台任务，系统可能会为了恢复内存而开始销毁后台 Activity，导致 Activity 状态丢失。

6. 如果 Activity A 启动 Activity B，Activity B 可在其清单中定义如何与当前任务相关联（如果关联的话），Activity A 也可以请求 Activity B 应该如何与当前任务关联。如果两个 Activity 都定义了 Activity B 应如何与任务关联，将优先遵循 Activity A 的请求（在 intent 中定义），而不是 Activity B 的请求（在清单中定义）。

7. `"singleTask"`

   系统会创建新任务，并实例化新任务的根 Activity。但是，如果另外的任务中已存在该 Activity 的实例，则系统会通过调用其 `onNewIntent()` 方法将 intent 转送到该现有实例，而不是创建新实例。Activity 一次只能有一个实例存在。

8. `"singleInstance"`

   与 `"singleTask"` 相似，唯一不同的是系统不会将任何其他 Activity 启动到包含该实例的任务中。该 Activity 始终是其任务唯一的成员；由该 Activity 启动的任何 Activity 都会在其他的任务中打开。

9. `FLAG_ACTIVITY_NEW_TASK`

   在新任务中启动 Activity。如果您现在启动的 Activity 已经有任务在运行，则系统会将该任务转到前台并恢复其最后的状态，而 Activity 将在 `onNewIntent()` 中收到新的 intent。

   这与上面介绍的 `"singleTask"` launchMode 值产生的行为相同。

10. `FLAG_ACTIVITY_SINGLE_TOP`

    如果要启动的 Activity 是当前 Activity（即位于返回堆栈顶部的 Activity），则现有实例会收到对 `onNewIntent()` 的调用，而不会创建 Activity 的新实例。

    这与上面介绍的 `"singleTop"` launchMode 值产生的行为相同。

11. `FLAG_ACTIVITY_CLEAR_TOP`

    如果要启动的 Activity 已经在当前任务中运行，则不会启动该 Activity 的新实例，而是会销毁位于它之上的所有其他 Activity，并通过 `onNewIntent()` 将此 intent 传送给它的已恢复实例（现在位于堆栈顶部）。

    launchMode 属性没有可产生此行为的值。

    `FLAG_ACTIVITY_CLEAR_TOP` 最常与 `FLAG_ACTIVITY_NEW_TASK` 结合使用。将这两个标记结合使用，可以查找其他任务中的现有 Activity，并将其置于能够响应 intent 的位置。

12. `taskAffinity` 属性采用字符串值，该值必须不同于 `<manifest>` 元素中声明的默认软件包名称，因为系统使用该名称来标识应用的默认任务亲和性。

13. 亲和性可在两种情况下发挥作用：

    - 当启动 Activity 的 intent 包含`FLAG_ACTIVITY_NEW_TASK标记时。

      默认情况下，新 Activity 会启动到调用 `startActivity()` 的 Activity 的任务中。它会被推送到调用方 Activity 所在的返回堆栈中。但是，如果传递给 `startActivity()` 的 intent 包含 `FLAG_ACTIVITY_NEW_TASK` 标记，则系统会寻找其他任务来容纳新 Activity。通常会是一个新任务，但也可能不是。如果已存在与新 Activity 具有相同亲和性的现有任务，则会将 Activity 启动到该任务中。如果不存在，则会启动一个新任务。

      如果此标记导致 Activity 启动一个新任务，而用户按下主屏幕按钮离开该任务，则必须为用户提供某种方式来返回到该任务。有些实体（例如通知管理器）总是在外部任务中启动 Activity，而不在它们自己的任务中启动，因此它们总是将 `FLAG_ACTIVITY_NEW_TASK` 添加到传递给 `startActivity()` 的 intent 中。如果您的 Activity 可由外部实体调用，而该实体可能使用此标记，请注意用户可以通过一种独立的方式返回到所启动的任务，例如使用启动器图标（任务的根 Activity 具有一个 `CATEGORY_LAUNCHER` intent 过滤器）。

    - 当 Activity 的 `allowTaskReparenting` 属性设为 `"true"` 时。

      在这种情况下，一旦和 Activity 有亲和性的任务进入前台运行，Activity 就可从其启动的任务转移到该任务。

      举例来说，假设一款旅行应用中定义了一个报告特定城市天气状况的 Activity。该 Activity 与同一应用中的其他 Activity 具有相同的亲和性（默认应用亲和性），并通过此属性支持重新归属。当您的某个 Activity 启动该天气预报 Activity 时，该天气预报 Activity 最初会和您的 Activity 同属于一个任务。不过，当旅行应用的任务进入前台运行时，该天气预报 Activity 就会被重新分配给该任务并显示在其中。

### 进程和应用生命周期

1. 进程生命周期错误的一个常见示例是当 `BroadcastReceiver` 在其 `BroadcastReceiver.onReceive()` 方法中接收到一个 Intent 时，它会启动一个线程，然后从该函数返回。一旦返回，则系统会认为 BroadcastReceiver 不再处于活动状态，因此不再需要其托管进程（除非其中有其他应用组件处于活动状态）。因此，系统可能会随时终止进程以回收内存，这样会终止在进程中运行的衍生线程。要解决这个问题，通常可以从 BroadcastReceiver 调度 `JobService`，这样系统就知道进程中还有处于活动状态的任务正在进行中。

### Parcelable 和 Bundle

1. 通过 intent 发送数据时，应小心地将数据大小限制为几 KB。发送过多数据会导致系统抛出 `TransactionTooLargeException` 异常。
2. Binder 事务缓冲区的大小固定有限，目前为 1MB，由进程中正在处理的所有事务共享。由于此限制是进程级别而不是 Activity 级别的限制，因此这些事务包括应用中的所有 binder 事务，例如 onSaveInstanceState，startActivity 以及与系统的任何互动。超过大小限制时，将引发 `TransactionTooLargeException`。
3. 在 Android 7.0（API 级别 24）或更高版本中，系统会在运行时抛出 TransactionTooLargeException 异常。在较低版本的 Android 中，系统仅在 logcat 中显示警告。

### Fragment

#### 概览

1. 片段的生命周期

   ![片段生命周期](/images/posts/android/fragment_lifecycle.png)

2. `onCreate()`

   系统会在创建片段时调用此方法。当片段经历暂停或停止状态继而恢复后，如果您希望保留此片段的基本组件，则应在您的实现中将其初始化。

3. `onCreateView()`

   系统会在片段首次绘制其界面时调用此方法。如要为您的片段绘制界面，您从此方法中返回的 `View` 必须是片段布局的根视图。如果片段未提供界面，您可以返回 null。

4. `onPause()`

   系统会将此方法作为用户离开片段的第一个信号（但并不总是意味着此片段会被销毁）进行调用。通常，您应在此方法内确认在当前用户会话结束后仍然有效的任何更改（因为用户可能不会返回）。

5. 在调用 `commit()` 之前，您可能希望调用 `addToBackStack()`，以将事务添加到片段事务返回栈。该返回栈由 Activity 管理，允许用户通过按*返回*按钮返回上一片段状态。

6. 向 `FragmentTransaction` 添加更改的顺序无关紧要，不过：

   - 您必须最后调用 `commit()`。
   - 如果您要向同一容器添加多个片段，则您添加片段的顺序将决定它们在视图层次结构中出现的顺序。

7. 如果您没有在执行删除片段的事务时调用 `addToBackStack()`，则事务提交时该片段会被销毁，用户将无法回退到该片段。不过，如果您在删除片段时调用 `addToBackStack()`，则系统会*停止*该片段，并随后在用户回退时将其恢复。

8. 对于每个片段事务，您都可通过在提交前调用 `setTransition()` 来应用过渡动画。

9. 注意：您只能在 Activity 保存其状态（当用户离开 Activity）之前使用 commit() 提交事务。如果您试图在该时间点后提交，则会引发异常。这是因为如需恢复 Activity，则提交后的状态可能会丢失。

10. 该片段的宿主 Activity 会实现 `OnArticleSelectedListener` 接口并重写 `onArticleSelected()`，将来自片段 A 的事件通知片段 B。为确保宿主 Activity 实现此接口，片段 A 的 `onAttach()` 回调方法（系统在向 Activity 添加片段时调用的方法）会通过转换传递到 `onAttach()` 中的 `Activity` 来实例化 `OnArticleSelectedListener` 的实例：

    ```java
    public static class FragmentA extends ListFragment {
    		// Container Activity must implement this interface
        public interface OnArticleSelectedListener {
            public void onArticleSelected(Uri articleUri);
        }
      	OnArticleSelectedListener listener;
        ...
        @Override
        public void onAttach(Context context) {
            super.onAttach(context);
            try {
                listener = (OnArticleSelectedListener) context;
            } catch (ClassCastException e) {
                throw new ClassCastException(context.toString() + " must implement OnArticleSelectedListener");
            }
        }
        ...
    }
    ```

11. 对于 Activity 生命周期与片段生命周期而言，二者最显著的差异是在其各自返回栈中的存储方式。默认情况下，Activity 停止时会被放入由系统管理的 Activity 返回栈中（以便用户通过*返回*按钮回退到 Activity）。不过，只有当您在移除片段的事务执行期间通过调用 `addToBackStack()` 显式请求保存实例时，系统才会将片段放入由宿主 Activity 管理的返回栈。

12. `onAttach()`

    在片段已与 Activity 关联时进行调用（`Activity` 传递到此方法内）。

13. `onActivityCreated()`

    当 Activity 的 `onCreate()` 方法已返回时进行调用。

14. `onDestroyView()`

    在移除与片段关联的视图层次结构时进行调用。

15. `onDetach()`

    在取消片段与 Activity 的关联时进行调用。

16. Activity 生命周期对片段生命周期的影响。

    ![Activity 生命周期对片段生命周期的影响](/images/posts/android/activity_fragment_lifecycle.png)