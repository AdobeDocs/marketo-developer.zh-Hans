---
title: 示例
description: 用于配置表单操作的Marketo代码示例
feature: Javascript
exl-id: dc5f0cc5-ff5a-48b0-be36-52c10e56f798
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '211'
ht-degree: 0%

---

# 示例

在下方，您可以找到一组具有演示性的Forms 2.0 Web窗体示例。

## 提交成功后隐藏表单

此示例不会将访客带到跟进页面或重新加载当前页面。

```javascript
MktoForms2.loadForm("//app-ab00.marketo.com", "785-UHP-775", 1057, function(form) {
    // Add an onSuccess handler
    form.onSuccess(function(values, followUpUrl) {
        // Get the form's jQuery element and hide it
        form.getFormElem().hide();
        // Return false to prevent the submission handler from taking the lead to the follow up url
        return false;
    });
});
```

## 将访客带入用户定义的URL

此示例在成功提交后将访客带到JavaScript确定的URL，而不是带入配置的感谢页面。

```javascript
MktoForms2.loadForm("//app-ab00.marketo.com", "785-UHP-775", 1057, function(form) {
    //Add an onSuccess handler
    form.onSuccess(function(values, followUpUrl) {
        // Take the lead to a different page on successful submit, ignoring the form's configured followUpUrl
        location.href = "https://google.com/?q=marketo+forms+v2+examples";
        // Return false to prevent the submission handler continuing with its own processing
        return false;
    });
});
```

## 设置表单字段值

此示例设置表单字段。

```javascript
MktoForms2.loadForm("//app-ab00.marketo.com", "785-UHP-775", 1057, function(form) {
    // Set the value of the Phone and Country fields
    form.vals({ "Phone":"555-555-1234", "Country":"USA"});
});
```

## 在表单提交时读取表单字段值

此示例读取表单提交中的表单字段。

```javascript
MktoForms2.loadForm("//app-ab00.marketo.com", "785-UHP-775", 1057, function(form) {
    // Add an onSubmit handler
    form.onSubmit(function(){
        // Get the form field values
        var vals = form.vals();
        // You may wish to call other function calls here, for example to fire google analytics tracking or the like
        // callSomeFunction(vals);
        // We'll just alert them to show the principle
        alert("Submitted values: " + JSON.stringify(vals));
    });
});
```

## 发生非表单点击事件时表单提交

此示例根据某个其他元素或事件上的点击事件提交一个表单，该元素或事件不属于该表单。

```javascript
// Load the form normally
MktoForms2.loadForm("//app-ab00.marketo.com", "785-UHP-775", 1057);

// Find the button element that you want to attach the event to
var btn = document.getElementById("MyAlternativeSubmitButtonId");
btn.onclick = function() {
    // When the button is clicked, get the form object and submit it
    MktoForms2.whenReady(function (form) {
        form.submit();
    });
};
```

## 阻止用户提交表单

对于此示例，您必须至少单击三次单击计数器按钮，表单上的提交按钮才能正常工作。

```javascript
MktoForms2.loadForm("//app-ab00.marketo.com", "785-UHP-775", 1057, function (form) {
    // Check if the form is submittable
    if (form.submittable()) {
        // Set it to be non submittable
        form.submittable(false);
    }
});

var clickCount = 0;
// Wire up the click counter button
var clickCounterBtn = document.getElementById("ClickCounter");
clickCounterBtn.onclick = function() {
    clickCount++;
    // Update the buttons's text
    clickCounterBtn.innerHTML = "Click Counter Button ("+clickCount+")";

    if (clickCount >= 3) {
        // Get the form and set it to be submittable
        MktoForms2.whenReady(function (form) {
            form.submittable(true);
        });
    }
};
```

## 在表单上的隐藏字段上设置值

此示例设置隐藏字段的值。

```javascript
MktoForms2.loadForm("//app-ab00.marketo.com", "785-UHP-775", 1057, function (form) {
    // Set values for the hidden fields, "userIsAwesome" and "enrollDate"
    // Note that these fields were configured in the form editor as hidden fields already
    form.vals({"userIsAwesome":"true", "enrollDate":"2014-01-01"});
});
```

## 在LightBox中显示表单

此示例在Lightbox样式对话框中显示表单（如果URL包含参数`lightboxForm=true`）。

```javascript
MktoForms2.loadForm("//app-ab00.marketo.com", "785-UHP-775", 1057, function (form) {
    if (location.href.indexOf("lightboxForm=true") != -1) {
        MktoForms2.lightbox(form).show();
    }
});
```

## 显示自定义错误消息

此示例显示基于自定义业务逻辑的提交自定义错误消息。

```javascript
MktoForms2.loadForm("//app-ab00.marketo.com", "785-UHP-775", 1057, function (form) {
    //listen for the validate event
    form.onValidate(function() {
        // Get the values
        var vals = form.vals();
        //Check your condition
        if (vals.Country == "USA" && vals.vehicleSize != "Massive") {
            // Prevent form submission
            form.submittable(false);
            // Show error message, pointed at VehicleSize element
            var vehicleSizeElem = form.getFormElem().find("#vehicleSize");
            form.showErrorMessage("All Americans must have a massive vehicle", vehicleSizeElem);
        }
        else {
            // Enable submission for those who met the criteria
            form.submittable(true);
        }
  });
});
```
