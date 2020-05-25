###如题的文件上传

#### html部分
```
 <div id="importViolationDiv">
        <el-dialog title="开票资料上传" :visible.sync="importAccidentDialog" width="30%">

            <el-upload
                    ref="uploadDocFile"
                    action="../../maintenance/uploadOverCompany"
                    :http-request="doNothing"
                    :on-change="addDocFile"
                    :before-upload="beforeUploadDocFile"
                    :on-remove="removeDocFile"
                    multiple
                    :limit="5"
                    :on-exceed="handleExceed">
                <el-button size="small" type="primary">添加文件</el-button>
            </el-upload>
            <div slot="footer" class="dialog-footer">
                <el-button @click="importAccidentDialog = false">取 消</el-button>
                <el-button type="primary" @click="uploadFile()">确 定</el-button>
            </div>
        </el-dialog>
    </div>
```


#### js部分
```         
data定义： 
            form: {
                       docFile: [],
                   }
方法methods:

            doNothing:function (file, fileList){
                // 为了使用上传文件之前的钩子before-upload，自定义此空函数，而不是设置auto-upload为false。
            },
            addDocFile:function(file, fileList) {
                if (file.status === 'ready') {
                    this.form.docFile.push(file.raw);
                }
            },
            beforeUploadDocFile:function(file) {
                var maxFileBytes = 400 * 1024 * 1024;
                var isSizeFit = file.size <= maxFileBytes;
                if (!isSizeFit) {
                    this.$message.error("做多只能上传400M");
                }
                return isSizeFit;
            },
            removeDocFile:function(file, fileList) {
                var index = this.form.docFile.indexOf(file.raw);
                this.form.docFile.splice(index, 1);
            },
            handleExceed:function(files, fileList) {
                this.$message.warning(`当前限制选择 5 个文件，本次选择了 ${files.length} 个文件，共选择了 ${files.length + fileList.length} 个文件`);
            },
             uploadFile:function () {
                            var that=this;
                            this.form["mainIds"] =that.selectIds.toString();
                            console.log(this.form);
                            var body = new FormData();
                            var data = this.form;
                            //这边不能直接用form，必须要转变成filed[]，这样子后台才能接收到，看图1
                            Object.keys(data).forEach(key => {
                                if (key === 'docFile') {
                                for (var value of data[key]) {
                                    body.append(key, value);
                                }
                            }else {
                                body.append(key, data[key]);
                            }
                        });
                            console.log(body.getAll('docFile'));
                            $.ajax({
                                url:"../../maintenance/uploadOverCompany",
                                type : 'POST',
                                processData : false,
                                contentType : false,
                                data : body,
                                success : function(data) {
                                    if(data.code == 200){
                                        that.$message({
                                            message: '上传成功',
                                            type: 'success'
                                        });
                                        
                                        that.importAccidentDialog=false;
                                    }else if(data.code == 500){
                                        that.$alert('报错原因:'+data.message, '上传失败', {
                                            confirmButtonText: '确定',
                                        });
                                    }
                                }
                            });
              },
```

- 图1![](1590374826(1).jpg)

#### 后台代码部分，用MultipartFile[]接收
```
@RequestMapping(value = "/uploadOverCompany" ,method = RequestMethod.POST)
    public JSONDataResult uploadOverCompany(@RequestPart(value = "docFile", required =true) MultipartFile[] docFile,@RequestParam("mainIds") String mainIds){
        StringBuffer stringBuffer = new StringBuffer();
        for(int i = 0 ; i<docFile.length;i++){
            if(null==docFile[i].getOriginalFilename()||docFile[i].getOriginalFilename()==""||docFile[i].getOriginalFilename().indexOf(".")==-1){
                return null;
            }
            String flag=ossServiceUtil.uploadObject(docFile[i]);
            if(flag.equals("error")){
                JSONDataResult jsonDataResult = new JSONDataResult();
                jsonDataResult.setCode(ResultConstant.FAIL_CODE);
                return jsonDataResult;
            }
            stringBuffer.append(flag+"$$");
        }
        return maintenanceServicel.updateMaintainOverState(mainIds,stringBuffer.toString());

    }

```