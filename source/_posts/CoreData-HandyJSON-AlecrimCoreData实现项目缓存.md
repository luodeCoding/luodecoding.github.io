---
title: CoreData+HandyJSON+AlecrimCoreData实现项目缓存
date: 2020-06-04 15:13:12
tags:
---
<h2>iOS封装CoreData+HandyJson实现本地缓存</h2>

> 准备工作

* <h5>选中Tagets->BuildPhases->LinkBinaryWithLibraries 添加CoreData.framework</h5>

* <h5>使用CocoaPods工具Pod需要使用的相关框架</h5>

		pod 'AlecrimCoreData'
		pod 'HandyJSON', '~> 5.0.1'
		
> CoreData基本使用查看上篇博客《CoreData数据持久化》

链接如下：
[CoreData数据持久化](https://luodecoding.github.io/2020/06/03/%E4%BD%BF%E7%94%A8CoreData%E5%81%9A%E9%A1%B9%E7%9B%AE%E6%95%B0%E6%8D%AE%E6%8C%81%E4%B9%85%E5%8C%96/)

> 配置 PersistentContainer

1. 创建PersistentContainer+App.swift
2. 代码如下
 
		import Foundation
		import AlecrimCoreData
		
		extension PersistentContainer {
		    public convenience init(name: String? = nil, bundle: Bundle? = nil) {
		        try! self.init(name: name,
		                       managedObjectModel: type(of: self).managedObjectModel(withName: name,
		                                                                             in: bundle ?? Bundle.main),
		                       storageType: .disk,
		                       persistentStoreURL: try! type(of: self).persistentStoreURL(withName: name, inPath: ""),
		                       persistentStoreDescriptionOptions: nil,
		                       ubiquitousConfiguration: nil)
		    }
		}

> 创建 StorageService.swift

代码如下

	import Foundation
	import AlecrimCoreData
	import CoreData
	import HandyJSON
	
	public final class StorageService: NSObject {
	    @objc public static let shared = StorageService()
	    private override init() {}
	    private lazy var container = PersistentContainer(name: "DataModel", bundle: nil)
	}
	
	extension NSManagedObjectContext {
	    fileprivate var dataModel: Query<DataModel> { return Query(in: self) }
	}
	
	extension StorageService {
	    public func set<T>(_ value: T, forKey defaultName: String) {
	        let v: String = {
	            switch value {
	            case let value as String:
	                return value
	            case let value as Int:
	                return String(value)
	            case let value as Float:
	                return String(value)
	            case let value as Double:
	                return String(value)
	            case let value as Bool:
	                return String(value)
	            case let value as URL:
	                return value.absoluteString
	            default:
	                fatalError("无效的类型")
	            }
	        }()
	
	        container.performBackgroundTask { context in
	            let query = context.dataModel.where { \.key == defaultName }
	            if let model = query.first() {
	                model.value = v
	            } else {
	                let model = DataModel(context: context)
	                model.key = defaultName
	                model.value = v
	            }
	
	            if context.hasChanges {
	                try? context.save()
	            }
	        }
	    }
	
	    private func _get(forKey defaultName: String) -> String? {
	        //        步骤一：获取总代理和托管对象总管
	        let context = container.viewContext
	
	        //        步骤二：建立一个获取的请求
	        let query = context.dataModel.where { \.key == defaultName }
	        if let result = query.first() {
	            return result.value
	        }
	
	        return nil
	    }
	
	    @objc public func remove(forKey defaultName: String) {
	        container.performBackgroundTask { context in
	            let fetchRequest: NSFetchRequest<DataModel> = DataModel.fetchRequest()
	            fetchRequest.predicate = NSPredicate(format: "key==%@", defaultName)
	            if let results = try? context.fetch(fetchRequest) {
	                for result in results {
	                    context.delete(result)
	                }
	
	                if context.hasChanges {
	                    try? context.save()
	                }
	            }
	        }
	    }
	
	    // MARK: - 普通类型
	
	    @objc public func isExist(forKey defaultName: String) -> Bool {
	        return _get(forKey: defaultName) != nil
	    }
	
	    @objc public func integer(forKey defaultName: String) -> Int {
	        let value = _get(forKey: defaultName)
	        return Int(value ?? "0") ?? 0
	    }
	
	    @objc public func float(forKey defaultName: String) -> Float {
	        let value = _get(forKey: defaultName)
	        return Float(value ?? "0") ?? 0
	    }
	
	    @objc public func double(forKey defaultName: String) -> Double {
	        let value = _get(forKey: defaultName)
	        return Double(value ?? "0") ?? 0
	    }
	
	    @objc public func string(forKey defaultName: String) -> String {
	        let value = _get(forKey: defaultName)
	        return value ?? ""
	    }
	
	    @objc public func bool(forKey defaultName: String) -> Bool {
	        let value = _get(forKey: defaultName)
	        return Bool(value ?? "") ?? false
	    }
	
	    @objc public func url(forKey defaultName: String) -> URL? {
	        let value = _get(forKey: defaultName)
	        return URL(string: value ?? "")
	    }
	}
	
	// MARK: - HandyJSON
	
	extension StorageService {
	    public func set<T>(_ value: T, forKey defaultName: String) where T: HandyJSON {
	        set(value.toJSONString()!, forKey: defaultName)
	    }
	
	    public func model<T>(forKey defaultName: String) -> T where T: HandyJSON {
	        let text: String = _get(forKey: defaultName) ?? ""
	        return T.deserialize(from: text) ?? T()
	    }
	
	    public func set<T>(_ value: [T], forKey defaultName: String) where T: HandyJSON {
	        set(value.toJSONString()!, forKey: defaultName)
	    }
	
	    public func models<T>(forKey defaultName: String) -> [T] where T: HandyJSON {
	        let text: String = _get(forKey: defaultName) ?? ""
	        return ([T].deserialize(from: text) ?? []) as! [T]
	    }
	}
	
	// MARK: - 仅用于OC
	
	extension StorageService {
	    @available(*, deprecated, message: "仅用于OC")
	    @objc public func setString(_ value: String, forKey defaultName: String) {
	        set(value, forKey: defaultName)
	    }
	}

> 总结

* <h5>利用AlecrimCoreData实现CoreData配置简易化</h5>
* <h5>HandyJson作为中介者，存储到本地的为JsonString字符串，Json字符串与Model或者Models的转换</h5>

[Demo链接](https://github.com/luodeCoding/RoderCoreData)

Roder, [我的博客](https://luodecoding.github.io/) 




		
