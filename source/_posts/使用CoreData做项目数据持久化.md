---
title: 使用CoreData做项目数据持久化
date: 2020-06-03 14:00:39
tags:
---

<h2>CoreData数据持久化</h2>

> 概念

<h5>CoreData是Apple官方为iOS提供的一个数据持久化方案，其本质是一个通过封装底层数据操作，让程序员以面向对象的方式存储和管理数据的ORM框架（Object-Relational Mapping：对象-关系映射，简称ORM）。虽然底层支持SQLite、二进制数据、xml等多种文件存储，但是主要还是用来操作SQLite数据库。</h5>

* <h5>NSManagedObjectContext 是托管对象上下文，数据库的大多数操作是在这个类操作</h5>
* <h5>NSManagedObjectModel 是托管对象模型，其中一个托管对象模型关联到一个模型文件，里面存储着数据库的数据结构。</h5>
* <h5>NSPersistentStoreCoordinator 是持久化存储协调器，主要负责协调上下文玉存储的区域的关系。</h5>
* <h5>NSManagedObject 是托管对象类，其中CoreData里面的托管对象都会继承此类。</h5>

> 创建相关文件
 
   <h4>两种方式</h4>
   
   * <h5>如果创建项目的时候就勾选了UseCoreData也就会自动生成一个.xcdatamodeld后缀的文件</h5>

   ![image](https://luodecoding.github.io/img/LearnCoreData/LearnCoreData01.png)
   
   * <h5>如果已有项目中就需要手动去创建一个TestModel文件</h5>

      ![image](https://luodecoding.github.io/img/LearnCoreData/LearnCoreData02.png)
   
> 配置TestModel

选中.xcdatamodeld文件

![image](https://luodecoding.github.io/img/LearnCoreData/LearnCoreData03.png)

1. 配置Person类的参数
2. 配置关联数据
3. 把它们看做数据库表 
4. 设置Codegen：

   * <h5>选择Manual/None就需要手动去创建对应的类文件（Robot+CoreDataClass 和 Robot+CoreDataProperties）</h5>
   * <h5>选择Class Definition会在编译时生成这两个文件，不需要自己定义对应的类型</h5>
   * <h5>选择Category/Extension，在编译时只会默认生成Robot+CoreDataProperties文件，需要手动创建Robot+CoreDataClass定义该类型</h5>

  注意：目前项目只用默认选择Class Definition

> 代码示例

首先在AppDelegate文件里面创建persistentContainer

		lazy var persistentContainer: NSPersistentContainer = {
		        let container = NSPersistentContainer(name: "TestModel")
		        container.loadPersistentStores { description, error in
		            if let error = error {
		                fatalError("Unable to load persistent stores: \(error)")
		            }
		        }
		        return container
		    }()
		    
在ViewController文件里面

1. 获取存储器上下文

		func getContext() -> NSManagedObjectContext {
				        let appDelegate = UIApplication.shared.delegate as! AppDelegate
				        return appDelegate.persistentContainer.viewContext
				    }

2. 批量写入数据

		func inssertClasses() {
		        for i in 1...100 {
		            let classId = Int16(i)
		            let name = "name"+"\(i)"
		            insertClass(classId: classId, name: name)
		        }
		    }

3. 写入数据

        func insertClass(classId:Int16,name:String) {

        //获取上下文对象

        let context = getContext()

        //两种方式创建数据
    
        //1.创建一个实例并赋值
        let personEntity = NSEntityDescription.insertNewObject(forEntityName: "Person", into: context) as! Person
        //Person对象赋值
        personEntity.id = classId
        personEntity.name = name

        //2.通过指定实体名 得到对象实例
        let Entity = NSEntityDescription.entity(forEntityName: "Person", in: context)
        let classEntity = NSManagedObject(entity: Entity!, insertInto: context)
        classEntity.setValue(classId, forKey: "id")
        classEntity.setValue(name, forKey: "name")

        //保存实体对象
        do {
            try context.save()
        } catch  {
            let nserror = error as NSError
            fatalError("错误:\(nserror),\(nserror.userInfo)")
        }
        
        //获取Model缓存文件路径
        print(NSSearchPathForDirectoriesInDomains(FileManager.SearchPathDirectory.applicationSupportDirectory, FileManager.SearchPathDomainMask.userDomainMask, true) )
	    }
	    
	  
4. 使用NavicatPremium工具通过路径查看数据模型

NavicatPremium工具：[传送门](https://xclient.info/s/navicat-premium.html)

![image](https://luodecoding.github.io/img/LearnCoreData/LearnCoreData04.png)
