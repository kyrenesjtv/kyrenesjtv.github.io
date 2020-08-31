### EXCEL里面增加下拉框选择，防止用户乱填数据

### 先create一个隐藏的sheet，用来做数据源，防止下拉框数据过长报错(String literals in formulas can't be bigger than 255 characters ASCII )
~~~
    private static HSSFDataValidation getDataValidationList4Col(HSSFSheet sheet, short firstRow,
                                                                short firstCol, short endRow,
                                                                short endCol, List<String> colName,
                                                                HSSFWorkbook wbCreat,int sheetIndex)
    {
        String[] dataArray = colName.toArray(new String[0]);
        HSSFSheet hidden = wbCreat.createSheet("hidden");
        HSSFCell cell = null;
        for (int i = 0, length = dataArray.length; i < length; i++)
        {
            String name = dataArray[i];
            HSSFRow row = hidden.createRow(i);
            cell = row.createCell(0);
            cell.setCellValue(name);
        }

        Name namedCell = wbCreat.createName();
        namedCell.setNameName("hidden");
        namedCell.setRefersToFormula("hidden!$A$1:$A$" + dataArray.length);
        //加载数据,将名称为hidden的
        DVConstraint constraint = DVConstraint.createFormulaListConstraint("hidden");

        // 设置数据有效性加载在哪个单元格上,四个参数分别是：起始行、终止行、起始列、终止列
        CellRangeAddressList addressList = new CellRangeAddressList(firstRow, endRow, firstCol,
                endCol);
        HSSFDataValidation validation = new HSSFDataValidation(addressList, constraint);

        //将对应sheet设置为隐藏
        wbCreat.setSheetHidden(sheetIndex, true);

        if (null != validation)
        {
            sheet.addValidationData(validation);
        }
        return validation;
    }
~~~

#### 
~~~
 HSSFDataValidation dataValidationList4Col = getDataValidationList4Col(sheet, (short) 1, (short) 2, (short) 6000, (short) 2, list1, workbook,1);
 //内容sheet
 sheet.addValidationData(dataValidationList4Col);
~~~