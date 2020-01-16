---
title: Kendo UI Export Excel
tag: KendoUI
---
**1.Config Export Button**

```jsx
<span class="btn btn-primary " style="float:left;margin-right:5px;" data-bind="click:excelExport">
  <i class="fa fa-file-excel-o" style="margin-right:3px;"></i>
  <@spring.message "hap.exportexcel"/>
</span>
```

**2.Config Grid**

```jsx
// add this attr in grid
excel: {
  fileName: "施工节点.xlsx",
  allPages: true, // Export all data, when paganation is active
}
```

**3.Config ViewModel**

```jsx
// Create this function in ViewModel's config
excelExport: function() {
  var grid = $('#notes_edit_grid').data('kendoGrid');
  grid.saveAsExcel();   
}
```

**PS:** 在配置好filed和title的情况下，无需其他多余操作，即可按照页面样式导出数据