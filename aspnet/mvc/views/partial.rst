:version: 1.0.0-rc2

Partial Views
=============

局部视图
============

By `Steve Smith`_

作者：`Steve Smith`_

翻译：`张海龙(jiechen) <http://github.com/ijiechen>`_ 、 `刘怡(AlexLEWIS) <http://github.com/alexinea>`_

校对：`许登洋(Seay) <https://github.com/SeayXu>`_、`何镇汐 <https://github.com/UtilCore>`_、`魏美娟(初见) <http://github.com/ChujianA>`_

ASP.NET Core MVC supports partial views, which are useful when you have reusable parts of web pages you want to share between different views.

ASP.NET Core MVC 支持局部视图，当你需要在多个不同视图间重用同一个页面部件时会显得特别有用。

.. contents:: Sections:
  :local:
  :depth: 1

`View or download sample code <https://github.com/aspnet/Docs/tree/master/aspnet/mvc/views/partial/sample>`__

`查看或下载示例代码 <https://github.com/aspnet/Docs/tree/master/aspnet/mvc/views/partial/sample>`__

What are Partial Views?
-----------------------

什么是局部视图？
----------------

A partial view is a view that is rendered within another view. The HTML output generated by executing the partial view is rendered into the calling (or parent) view. Like views, partial views use the *.cshtml* file extension.

局部视图是在其它视图中被渲染的视图。局部视图执行后生成的 HTML 结果会被渲染到调用方视图或父视图中。跟视图文件一样，局部视图文件也使用 *.cshtml* 作为文件扩展名。

.. note:: If you're coming from an ASP.NET Web Forms background, partial views are similar to `user controls <https://msdn.microsoft.com/en-us/library/y6wb1a0e.aspx>`_.

.. note:: 如果你有 ASP.NET Web Forms 的开发背景，那么局部视图就比较类似于你以前用过的 `用户控件 <https://msdn.microsoft.com/en-us/library/y6wb1a0e.aspx>`_ 。

When Should I Use Partial Views?
--------------------------------

何时使用局部视图？
------------------------------

Partial views are an effective way of breaking up large views into smaller components. They can reduce duplication of view content and allow view elements to be reused. Common layout elements should be specified in :doc:`_Layout.cshtml <layout>`. Non-layout reusable content can be encapsulated into partial views.

局部视图是将大视图分解为小组件的有效方式。它可以减少视图内容的重复并允许视图元素复用。通用的布局元素应该写在 :doc:`_Layout.cshtml <layout>` 中。非布局（non-layout）的重用内容则可封装到局部视图之中。

If you have a complex page made up of several logical pieces, it can be helpful to work with each piece as its own partial view. Each piece of the page can be viewed in isolation from the rest of the page, and the view for the page itself becomes much simpler since it only contains the overall page structure and calls to render the partial views.

如果你有一个由多个逻辑块构成的复杂页面，那么将每个逻辑块都作为局部视图是很有用的。页面的每一个部分都可视为独立于其他部分，而页面本身也会变得简单很多，因为它只包含页面的整体结构并调用渲染各局部视图。

.. tip:: Follow the `Don't Repeat Yourself Principle <http://deviq.com/don-t-repeat-yourself/>`_ in your views.

.. tip:: 在你的视图中遵从 `不要使自己重复原则 <http://deviq.com/don-t-repeat-yourself/>`_ 。

Declaring Partial Views
-----------------------

定义局部视图
-------------

Partial views are created like any other view: you create a *.cshtml* file within the *Views* folder. There is no semantic difference between a partial view and a regular view - they are just rendered differently. You can have a view that is returned directly from a controller's :dn:class:`~Microsoft.AspNetCore.Mvc.ViewResult`, and the same view can be used as a partial view. The main difference between how a view and a partial view are rendered is that partial views do not run *_ViewStart.cshtml* (while views do - learn more about *_ViewStart.cshtml* in :doc:`layout`).

创建局部视图与创建其它视图类似：你在 *Views* 文件夹中添加一个 *.cshtml* 文件。局部视图与普通视图之间没有语义级的区别，它们只是渲染上有所不同。你可以直接从控制器的 :dn:class:`~Microsoft.AspNetCore.Mvc.ViewResult` 返回一个视图，而这个视图也可当做局部视图来用。两类视图的主要区别在于渲染上的不同：局部视图不会运行 *_ViewStart.cshtml* （普通视图则会运行。了解更多 *_ViewStart.cshtml* 的信息请访问 :doc:`layout`）。

Referencing a Partial View
--------------------------

引用局部视图
-------------

From within a view page, there are several ways in which you can render a partial view. The simplest is to use ``Html.Partial``, which returns an ``IHtmlString`` and can be referenced by prefixing the call with ``@``:

有多种方法在视图中渲染局部视图。最简单的办法是使用 ``Html.Partial``，它通过 ``@`` 前缀来调用并返回 ``IHtmlContent`` ：

.. literalinclude:: partial/sample/src/PartialViewsSample/Views/Home/About.cshtml
  :lines: 9

The :dn:method:`~Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.PartialAsync` method is available for partial views containing asynchronous code (although code in views is generally discouraged):

:dn:method:`~Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.PartialAsync` 方法对包含异步代码（尽管通常在视图中不推荐这么做）的局部视图是可用的。

.. literalinclude:: partial/sample/src/PartialViewsSample/Views/Home/About.cshtml
  :lines: 8

You can render a partial view with :dn:method:`~Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial`. This method doesn't return a result; it streams the rendered output directly to the response. Because it doesn't return a result, it must be called within a Razor code block (you can also call ``RenderPartialAsync`` if necessary):

可以使用 :dn:method:`~Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial` 渲染局部视图。这个方法不返回结果；它将渲染结果直接输出到响应中。正因为它不返回结果，所以必须在 Razor 代码块中调用（当然如果有必要，你也可以调用 ``RenderPartialAsync`` 方法）：

.. literalinclude:: partial/sample/src/PartialViewsSample/Views/Home/About.cshtml
  :lines: 10-12

Because it streams the result directly, ``RenderPartial`` and ``RenderPartialAsync`` may perform better in some scenarios. However, in most cases it's recommended you use ``Partial`` and ``PartialAsync``.

因为会直接输出结果， ``RenderPartial`` 和 ``RenderPartialAsync`` 方法可能在一些场景下表现更佳。但是，在大多数情况下推荐你使用 ``Partial`` 和 ``PartialAsync`` 这两个方法。

.. note:: If your views need to execute code, the recommended pattern is to use a :doc:`view component <view-components>` instead of a partial view.

.. note:: 如果你的视图需要执行代码，推荐你使用 :doc:`视图组件 <view-components>` 来替代局部视图。

Partial View Discovery
^^^^^^^^^^^^^^^^^^^^^^

发现局部视图
^^^^^^^^^^^^^^^

When referencing a partial view, you can refer to its location in several ways:

当引用局部视图的时候，你可以通过多种方式找到它的位置：

.. code-block:: text

  // Uses a view in current folder with this name
  // 以视图名使用当前文件夹下的视图
  // If none is found, searches the Shared folder
  // 如果没有找到，则搜索 Shared 文件夹
  @Html.Partial("ViewName")

  // A view with this name must be in the same folder
  // 这个名称的视图必须在相同文件夹下
  @Html.Partial("ViewName.cshtml")

  // Locate the view based on the application root
  // 依据应用根路径定位视图
  // Paths that start with "/" or "~/" refer to the application root
  // 以 "/" 或 "~/" 开头的路径代表应用根路径
  @Html.Partial("~/Views/Folder/ViewName.cshtml")
  @Html.Partial("/Views/Folder/ViewName.cshtml")

  // Locate the view using relative paths
  // 使用相对路径定位视图
  @Html.Partial("../Account/LoginPartial.cshtml")

If desired, you can have different partial views with the same name in different view folders. When referencing the views by name (without file extension), views in each folder will use the partial view in the same folder with them. You can also specify a default partial view to use, placing it in the *Shared* folder. This view will be used by any views that don't have their own copy of the partial view in their folder. In this way, you can have a default partial view (in *Shared*), which can be overridden by a partial view with the same name in the same folder as the parent view.

若需要，你可以在不同的视图文件夹下存放同名的不同局部视图。当根据视图名称（不包括文件扩展名）来引用视图时，视图将使用其所在的文件夹下的局部视图。当然你也可以指定并使用默认位于 *Shared* 文件夹下的局部视图，这个视图可被任意视图（前提是在它们的文件夹下不存在这个局部视图）使用。换句话说，你可以在 *Shared* 文件夹下放置默认的局部视图，该默认局部视图会被当前执行视图所在文件夹下的同名局部视图所覆盖。

Partial views can be *chained*. That is, a partial view can call another partial view (as long as you don't create a loop). Within each view or partial view, relative paths are always relative to that view, not the root or parent view.

局部视图可以被连续地链式使用 。这个意思是说一个局部视图可以调用另一个局部视图（只要你不要创建循环）。在每个视图或局部视图中，相对路径总是相对于所在视图，而非根路径或父视图。

.. note:: If you declare a :doc:`Razor <razor>` ``section`` in a partial view, it will not be visible to its parent(s); it will be limited to the partial view.

.. note:: 如果你在局部视图中定义 :doc:`Razor <razor>` ``section`` ，将对其父级不可见；将被限定在局部视图。

Accessing Data From Partial Views
---------------------------------

从局部视图访问数据
----------------------

When a partial view is instantiated, it gets a copy of the parent view's ``ViewData`` dictionary. Updates made to the data within the partial view are not persisted to the parent view. ``ViewData`` changed in a partial view is lost when the partial view returns.

当局部视图被实例化，它获得父视图的 ``ViewData`` 字典的副本。在局部视图中对该字典副本进行的修改不会影响到父视图中的字典。 当局部视图返回时，将丢弃局部视图中的 ``ViewData`` 副本。

You can pass an instance of ``ViewDataDictionary`` to the partial view:

你可以传递 ``ViewDataDictionary`` 的实例到局部视图：

.. code-block:: c#

  @Html.Partial("PartialName", customViewData)

You can also pass a model into a partial view. This can be the page's view model, or some portion of it, or a custom object. Simply pass in the model as the second parameter when calling ``Partial``/``PartialAsync`` or ``RenderPartial``/``RenderPartialAsync``:

你也可以传递模型到局部视图。该模型可以是页面的视图模型（view model），也可以是视图模型的一部分，亦或者是其他自定义对象。只需要在调用 ``Partial``/``PartialAsync`` 或 ``RenderPartial``/``RenderPartialAsync`` 时简单地把模型作为第二个参数传入。

.. code-block:: c#

  @Html.Partial("PartialName", viewModel)

You can pass an instance of ``ViewDataDictionary`` and a view model to a partial view:

你可以传递一个 ``ViewDataDictionary`` 的实例和视图模型到局部视图：

.. code-block:: c#

  @Html.Partial("PartialName", viewModel, customViewData)

An Example
^^^^^^^^^^

示例
^^^^^^^^^^^^

The following view specifies a view model of type ``Article``. ``Article`` has an ``AuthorName`` property that is passed to a partial view named *AuthorPartial*, and a property of type ``List<ArticleSection>``, which is passed (in a loop) to a partial devoted to rendering that type:

下例中，视图指定了 ``Article`` 类型的视图模型。 ``Article`` 有一个 ``AuthorName`` 属性，它被传递到一个叫做 *AuthorPartial* 的局部视图中；同时还有一个 ``List<ArticleSection>`` 类型的属性，它被传递到一个专用于渲染此类型的局部视图：

.. literalinclude:: partial/sample/src/PartialViewsSample/Views/Articles/Read.cshtml
  :emphasize-lines: 2, 5, 10

The *AuthorPartial* (which in this case is in the */Views/Shared* folder):

 *AuthorPartial* （此例中在 */Views/Shared* 文件夹）：

.. literalinclude:: partial/sample/src/PartialViewsSample/Views/Shared/AuthorPartial.cshtml
  :emphasize-lines: 1

The *ArticleSection* partial:

 *ArticleSection* 部分：

.. literalinclude:: partial/sample/src/PartialViewsSample/Views/Articles/ArticleSection.cshtml
  :emphasize-lines: 2

At runtime, the partials are rendered into the parent view, which itself is rendered within the shared *_Layout.cshtml*, resulting in output like this:

在运行时，这些局部视图将被渲染到父视图，父视图自身在共享的 *_Layout.cshtml* 中被渲染，输出结果如下：

.. image:: partial/_static/output.png
