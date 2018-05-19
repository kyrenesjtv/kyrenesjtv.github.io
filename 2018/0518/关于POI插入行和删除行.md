## POI插入行和删除行
很多时候我们在进行poi导出的时候会遇到再中途突然要插入一行数据，会比较烦对不对。实际上很简单，只要获取到最后一行的row num然后将当前行整体往下移动一行即可。
删除也是同样的意思，只不过是整体网上移动一行。

#### 新增一行
```
/**
     * 找到需要插入的行数，并新建一个POI的row对象
     * @param sheet
     *          当前sheet页的对象
     * @param rowIndex
     *          要插入的当前行数
     * @return
     */
    private static HSSFRow createRow(HSSFSheet sheet, Integer rowIndex) {
        HSSFRow row = null;
        if (sheet.getRow(rowIndex) != null) {
            int lastRowNo = sheet.getLastRowNum();
            sheet.shiftRows(rowIndex, lastRowNo, 1);// rowIndex 当前行 lastRowNo 末尾行 1 往下移动一行 (正数代表往下移动，负数表示往上移动)
        }
        row = sheet.createRow(rowIndex);
        return row;
    }
```

#### 删除一行
```
    /**
     * 找到需要删除的行数，并新建一个POI的row对象
     * @param sheet
        *          当前sheet页的对象
        * @param rowIndex
        *          要插入的当前行数
        * @return
        */
       private static void createRow(HSSFSheet sheet, Integer rowIndex) {
           if (sheet.getRow(rowIndex) != null) {
               int lastRowNo = sheet.getLastRowNum();
               sheet.shiftRows(rowIndex, lastRowNo, -1);// rowIndex 当前行 lastRowNo 末尾行 1 往下移动一行 (正数代表往下移动，负数表示往上移动)
           }
       }
```


