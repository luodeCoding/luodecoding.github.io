---
title: RoForm自定义表单
date: 2022-02-11 10:08:00
tags:
---

# RoForm自定义表单

###### 自定义表单工具，简单配置，实现清晰，快速创建表单

> 效果

![image](https://gitee.com/SwagGroup/image-storage/raw/master/imageFolder/formDemo.gif)



> 如何导入

1. ###### 项目中导入NEFormTableView，UIHelper，Vender等文件夹；

2. ###### pod中有依赖
   
   ```
   pod 'Masonry', '~> 1.1.0'
   
   pod 'BRPickerView', '~> 2.7.6'
   
   pod 'HCSStarRatingView', '~> 1.5'
   
   pod 'QMUIKit', '~> 4.4.0'
   
   pod 'ReactiveObjC', '~> 3.1.0'
   
   pod 'SDWebImage', '~> 5.0'
   ```

3. ###### 库中包含了图片选择所以需要相册相机权限
   
   Privacy - Photo Library Usage Description 授权通过相册，选择头像或身份证照片
   
   Privacy - Camera Usage Description 授权通过相机，进行头像拍摄或扫描二维码

>  使用

1. ###### 初始化表单ViewModel

```objectivec
NECreateDemoViewModel *viewModel = [[NECreateDemoViewModel alloc]init];
```

```objectivec
// 表单数组
@property (nonatomic, strong) NSArray<NSArray*>* modelGroups;
```

```objectivec
- (NSArray<NSArray*>*)modelGroups{
    if (!_modelGroups) {
        // 根据需求添加不同的formCellModel
        _logoImagesModel = [[NEImagePickTableViewCellModel alloc] init];
        _logoImagesModel.title = @"上传俱乐部Logo";
        _logoImagesModel.mode = 1;
        _logoImagesModel.maxPhotoNum = 1;
        _logoImagesModel.isNecessary = YES;
        // 装进组内
        self.modelGroups = @[@[_logoImagesModel]];
    }
    return _modelGroups;
}
```

2. ###### 初始化表单TableView
   
   ```objectivec
   [[NEFormTableView alloc] initWithFrame:CGRectZero style:UITableViewStyleGrouped models:self.viewModel.modelGroups];
   ```

3. ###### 可以根据自己的业务调整Cell样式和内部逻辑，也可以自定义其他不同的cell；

> 关于

[Demo链接](https://github.com/luodeCoding/RoForm)

Roder, [我的博客](https://luodecoding.github.io/)
