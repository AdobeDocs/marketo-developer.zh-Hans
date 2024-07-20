---
title: Forms API参考
description: Forms API参考
feature: Forms, Javascript
exl-id: 0f8d242f-0b27-4087-b080-3d41ebaa25b3
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1327'
ht-degree: 1%

---

# Forms API参考

您将使用Forms 2.0 API与以下两个主要对象进行交互。 `MktoForms2`对象和`Form`对象。 `MktoForms2`对象是Forms2功能的顶级公开可见命名空间，包含用于创建、加载和提取表单对象的函数。

## MktoForms2方法

<table>
  <tbody>
    <tr valign="top">
      <td><strong>方法</strong></td>
      <td><strong>描述</strong></td>
      <td><strong>参数</strong></td>
      <td><strong>返回值</strong></td>
    </tr>
    <tr valign="top">
      <td>.loadForm(baseUrl， munchkinId， formId， callback)</td>
      <td>从Marketo服务器加载表单描述符并创建新表单对象。</td>
      <td> baseUrl(String) — 指向您的订阅的Marketo服务器实例的URL</td>
      <td>未定义</td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>munchkinId（字符串） — 订阅的Munchkin ID</td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>formId（字符串或数字） — 要加载的表单的表单版本ID (Vid)</td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>callback （可选）（函数） — 一个回调函数，在加载和初始化所构造的Form对象后将传递给。</td>
      <td></td>
    </tr>
    <tr valign="top">
      <td>.lightbox（表单，选项）</td>
      <td>呈现带有表单对象的灯箱样式模式对话框。</td>
      <td>表单（表单对象） — 您希望在Lightbox中渲染的表单对象的实例。</td>
      <td>具有.show()和.hide()方法的灯箱对象。</td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>opts（可选）（对象） — 传递给灯箱对象的选项对象</td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>onSuccess(Function) — 在提交表单时触发的回调。</td>
      <td></td>
    </tr>
    <tr>
          <td></td>
      <td></td>
      <td>closeBtn(Boolean)默认值true — 控制灯箱对话框中是否显示关闭按钮(X)。</td>
      <td></td>
    </tr>
    <tr valign="top">
      <td>.newForm(formData， callback)</td>
      <td>从表单描述符JS对象创建新的表单对象。 添加回调函数，在获取所有样式表和已知潜在客户信息并创建Form对象后调用该函数。</td>
      <td>formData（表单描述符对象） — 由Marketo Forms V2编辑器创建的表单描述符对象</td>
      <td>未定义</td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>callback （可选）（函数） — 使用单个参数调用此回调，该参数是新创建的Form对象实例。</td>
      <td></td>
    </tr>
    <tr valign="top">
      <td>.getForm(formId)</td>
      <td>通过表单标识符获取之前创建的表单对象</td>
      <td> formId（数字或字符串） — 表单Vid标识符。</td>
      <td>表单对象</td>
    </tr>
    <tr valign="top">
      <td>.allForms()</td>
      <td>获取之前已在页面上构建的所有表单对象的数组。</td>
      <td>不适用</td>
      <td>表单对象的数组</td>
    </tr>
    <tr valign="top">
      <td>.getPageFields()</td>
      <td>从URL和反向链接中获取可能用于跟踪目的的数据的JS对象。</td>
      <td>不适用</td>
      <td>对象</td>
    </tr>
    <tr valign="top">
      <td>.whenReady(callback)</td>
      <td>为页面上的“就绪”每个表单添加一次回调。 就绪意味着表单存在、已初次呈现并且已调用其初始回调。 如果调用此函数时已存在已准备好的表单，则会立即调用传递的回调。</td>
      <td>callback(Function) — 传递回调的是一个参数，即表单对象。</td>
      <td>MktoForms2对象</td>
    </tr>
    <tr valign="top">
      <td>.onFormRender(callback)</td>
      <td>添加每次呈现页面上的任何表单时调用的回调。 Forms在最初创建时进行渲染，然后每当可见性规则改变表单的结构时再次渲染。</td>
      <td>callback（函数） — 传递一个参数，该参数是已渲染表单的表单对象。</td>
      <td>MktoForms2对象</td>
    </tr>
    <tr valign="top">
      <td>.whenRendered(callback)</td>
      <td>与onFormRender类似，此选项会添加每次渲染表单时调用的回调。 此外，这还会立即为已渲染的所有表单调用回调。</td>
      <td>callback(Function) - callback传递一个参数，该参数是已渲染表单的表单对象。</td>
      <td></td>
    </tr>
</table>


## 表单方法

<table>
  <tbody>
    <tr valign="top">
      <td><strong>方法</strong></td>
      <td><strong>描述</strong></td>
      <td><strong>参数</strong></td>
      <td><strong>返回值</strong></td>
    </tr>
    <tr valign="top">
      <td>.render(formElem)</td>
      <td>渲染表单对象，返回封装包含表单的表单元素的jQuery对象。 如果传递了formElem，则会将其用作表单元素，否则会创建一个新表单元素。</td>
      <td>formElem（可选） — 要呈现到其中的jQuery对象封装表单元素。</td>
      <td> 包含渲染表单的jQuery对象包装表单元素。</td>
    </tr>
    <tr valign="top">
      <td>.getId()</td>
      <td>获取表单的ID。</td>
      <td>不适用</td>
      <td>数字 — 此表单表示的表单对象的ID</td>
    </tr>
    <tr valign="top">
      <td>.getFormElem()</td>
      <td>获取已渲染表单的jQuery包装表单元素。</td>
      <td>不适用</td>
      <td>jQuery对象封装的表单元素；如果表单尚未使用render()方法渲染，则为null。</td>
    </tr>
    <tr valign="top">
      <td>.validate()</td>
      <td>强制验证表单，突出显示可能存在的任何错误并返回结果。 不提交表单。</td>
      <td>不适用</td>
      <td>布尔值 — 如果传递的表单上的所有验证器，则返回true；否则，返回false。</td>
    </tr>
    <tr valign="top">
      <td>.onValidate(callback)</td>
      <td>添加验证回调，每次触发验证时都会调用该回调。</td>
      <td>callback(Function) — 在任何验证发生时触发的回调。 将向回调传递一个参数，该参数是一个布尔值，用于指示验证是否成功。</td>
      <td>表单对象 — 用于链接目的的调用方法的相同表单对象。</td>
    </tr>
    <tr valign="top">
      <td>.submit()</td>
      <td>触发表单的提交事件。 此操作将从提交流程开始，执行验证，触发任何onSubmit事件，提交表单，并在表单提交成功时触发任何onSuccess事件。</td>
      <td>不适用</td>
      <td>表单对象 — 用于链接目的的调用方法的相同表单对象。</td>
    </tr>
    <tr valign="top">
      <td>.onSubmit(callback)</td>
      <td>添加在提交表单时将调用的回调。 当提交开始时，在知道请求是否“成功”之前，会触发此事件。</td>
      <td>callback — 提交表单时将调用的函数。 此回调将传递一个参数，即此Form对象。</td>
      <td>表单对象 — 用于链接目的的调用方法的相同表单对象。</td>
    </tr>
    <tr valign="top">
      <td>.onSuccess(callback)</td>
      <td>添加一个回调，该回调将在成功提交表单时但在潜在客户转发到后续页面之前调用。 可用于防止在成功提交后将商机转发到后续页面。</td>
      <td>callback — 成功提交表单时将调用的函数。 此回调将传递两个参数。 一个JS对象，其中包含已提交的值和用户将转发到的跟进页面的字符串URL；如果没有已配置的跟进页面，则包含Null或空字符串。 特殊行为：如果此回调返回“false”(使用===测量)，则访客将不会转发到跟进页面，并且也不会重新加载页面。 这允许实施者对跟进URL执行额外的处理，或在使用JavaScript而不是离开页面的页面上执行操作。</td>
      <td>表单对象 — 用于链接目的的调用方法的相同表单对象。</td>
    </tr>
    <tr valign="top">
      <td>.submittable(canSubmit) <em>也可用为： </em> <em>.submitable(canSubmit)</em></td>
      <td>获取或设置是否可以提交表单。 如果使用无参数调用，则它将获取值；如果使用一个参数调用，则它将设置值。这可用于阻止提交表单，而必须满足普通表单之外的其他条件。</td>
      <td>canSubmit （可选）（布尔值） — 将表单设置为可提交或不可提交。</td>
      <td>布尔值或表单对象 — 如果使用无参数调用，则返回一个布尔值，指示表单是否可提交。 如果使用某个参数调用，则返回此表单对象以进行链接。 </td>
    </tr>
    <tr valign="top">
      <td>.allFieldsFilled()</td>
      <td>如果表单中的所有字段都设置了非空值，则返回true。</td>
      <td>不适用</td>
      <td>布尔值 — 如果所有字段都具有非空白/空/未设置/空值，则为True；否则为false。</td>
    </tr>
    <tr valign="top">
      <td>.setValues(vals)</td>
      <td>设置表单中一个或多个字段的值。</td>
      <td>vals - JS对象。 对于对象中的每个键/值对，名为key的表单字段将设置为value。</td>
      <td>未定义</td>
    </tr>
    <tr valign="top">
      <td>.getValues()</td>
      <td>获取表单中所有字段的所有值。</td>
      <td>不适用</td>
      <td>对象 — 包含键/值对的JS对象，表示表单中字段的名称和值。</td>
    </tr>
    <tr valign="top">
      <td>.addHiddenFields(values)</td>
      <td>向表单添加输入type=hidden字段。</td>
      <td>values - JS对象，其中包含键/值对，表示要添加到表单的隐藏字段的名称和值。</td>
      <td>未定义</td>
    </tr>
    <tr valign="top">
      <td>.vals(values)</td>
      <td>jQuery样式.vals() setter/getter。 如果使用无参数调用，则等效于调用getValues()。 如果使用一个参数调用，则等效于调用setValues()</td>
      <td>值（可选） — 对象</td>
      <td>未定义</td>
    </tr>
    <tr valign="top">
      <td>.showErrorMessage(msg， elem)</td>
      <td>显示一条错误消息，指向元素。</td>
      <td>msg(HTML字符串) — 包含要显示的错误文本的字符串。</td>
            <td>表单对象 — 此表单对象，用于链接。</td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>elem （可选）（jQuery对象） — 错误要指向的元素。 如果未设置，则使用表单的提交按钮。</td>
<td></td>
    </tr>
  </tbody>
</table>
